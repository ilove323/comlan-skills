# 使用PeakVI进行scATAC-seq分析

本参考文档介绍使用PeakVI进行单细胞ATAC-seq分析，包括降维、批次校正和差异可及性分析。

## 概述

PeakVI是一种用于scATAC-seq数据的深度生成模型，它：
- 对二元可及性（peak开放/关闭）进行建模
- 处理批次效应
- 提供用于聚类的潜在表示
- 支持差异可及性分析

## 先决条件

```python
import scvi
import scanpy as sc
import numpy as np
import anndata as ad

print(f"scvi-tools version: {scvi.__version__}")
```

## 第1步：加载并准备ATAC数据

### 来自10x Genomics（Cell Ranger ATAC）

```python
# 来自片段的peak-cell矩阵
# 通常为filtered_peak_bc_matrix格式

adata = sc.read_10x_h5("filtered_peak_bc_matrix.h5")

# 或来自mtx格式
adata = sc.read_10x_mtx("filtered_peak_bc_matrix/")

# 检查结构
print(f"Cells: {adata.n_obs}, Peaks: {adata.n_vars}")
print(f"Sparsity: {1 - adata.X.nnz / (adata.n_obs * adata.n_vars):.2%}")
```

### 来自ArchR/Signac

```python
# 从ArchR导出（在R中）
# saveArchRProject(proj, outputDirectory="atac_export", load=FALSE)
# 然后在Python中读取导出的文件

# 来自Signac：
# 导出peak矩阵和元数据
```

## 第2步：质量控制

```python
# 计算QC指标
sc.pp.calculate_qc_metrics(adata, inplace=True)

# ATAC的关键指标：
# - n_genes_by_counts：每个细胞的peak数（应重命名）
# - total_counts：每个细胞的片段数
adata.obs['n_peaks'] = adata.obs['n_genes_by_counts']
adata.obs['total_fragments'] = adata.obs['total_counts']

# 过滤细胞
adata = adata[adata.obs['n_peaks'] > 500].copy()
adata = adata[adata.obs['n_peaks'] < 50000].copy()  # 删除潜在的双细胞

# 过滤peak（至少在n个细胞中可及）
sc.pp.filter_genes(adata, min_cells=10)

print(f"After QC: {adata.shape}")
```

### 二值化数据

```python
# PeakVI使用二元可及性
# 如果尚未二值化，则进行二值化
adata.X = (adata.X > 0).astype(np.float32)

# 验证
print(f"Unique values: {np.unique(adata.X.data)}")
```

## 第3步：特征选择

与RNA-seq不同，ATAC的peak选择方法尚不成熟。选项：

### 选项A：最可及的peak

```python
# 按可及性频率选择顶部peak
peak_accessibility = np.array(adata.X.sum(axis=0)).flatten()
top_peaks = np.argsort(peak_accessibility)[-50000:]  # 前50k个peak

adata = adata[:, top_peaks].copy()
```

### 选项B：可变peak

```python
# 选择高方差的peak
# （对聚类最具信息量）
from sklearn.feature_selection import VarianceThreshold

selector = VarianceThreshold(threshold=0.05)
selector.fit(adata.X)
adata = adata[:, selector.get_support()].copy()
```

### 选项C：基因附近的peak

```python
# 保留启动子区域或基因主体附近的peak
# 需要peak注释
# gene_peaks = 有基因注释的peak
# adata = adata[:, adata.var['near_gene']].copy()
```

## 第4步：添加批次信息

```python
# 如果有多个样本，添加批次注释
adata.obs['batch'] = adata.obs['sample_id']  # 或适当的列

print(adata.obs['batch'].value_counts())
```

## 第5步：设置并训练PeakVI

```python
# 设置AnnData
scvi.model.PEAKVI.setup_anndata(
    adata,
    batch_key="batch"  # 可选，单批次时省略
)

# 创建模型
model = scvi.model.PEAKVI(
    adata,
    n_latent=20,      # 潜在维度
    n_layers_encoder=2,
    n_layers_decoder=2
)

# 训练
model.train(
    max_epochs=200,
    early_stopping=True,
    batch_size=128
)

# 检查训练
model.history['elbo_train'].plot()
```

## 第6步：获取潜在表示

```python
# 用于下游分析的潜在空间
adata.obsm["X_PeakVI"] = model.get_latent_representation()

# 聚类和可视化
sc.pp.neighbors(adata, use_rep="X_PeakVI", n_neighbors=15)
sc.tl.umap(adata)
sc.tl.leiden(adata, resolution=0.5)

# 可视化
sc.pl.umap(adata, color=['leiden', 'batch'], ncols=2)
```

## 第7步：差异可及性

```python
# 聚类间的差异可及性
da_results = model.differential_accessibility(
    groupby='leiden',
    group1='0',
    group2='1'
)

# 过滤显著peak
da_sig = da_results[
    (da_results['is_da_fdr_0.05']) &
    (abs(da_results['lfc_mean']) > 1)
]

print(f"Significant DA peaks: {len(da_sig)}")
print(da_sig.head())
```

### 条件间的差异可及性

```python
# 在细胞类型内比较条件
adata_subset = adata[adata.obs['cell_type'] == 'CD4 T cells'].copy()

da_condition = model.differential_accessibility(
    groupby='condition',
    group1='treated',
    group2='control'
)
```

## 第8步：peak注释

```python
# 用最近基因注释peak
# 使用pybedtools或类似工具

# peak名称格式示例：chr1:1000-2000
# 解析为bed格式用于注释

import pandas as pd

def parse_peak_names(peak_names):
    """将peak名称解析为bed格式。"""
    records = []
    for peak in peak_names:
        chrom, coords = peak.split(':')
        start, end = coords.split('-')
        records.append({
            'chrom': chrom,
            'start': int(start),
            'end': int(end),
            'peak': peak
        })
    return pd.DataFrame(records)

peak_bed = parse_peak_names(adata.var_names)
```

## 第9步：基序分析

```python
# 导出显著peak用于基序分析
# 使用HOMER、MEME或chromVAR

# 导出peak序列
sig_peaks = da_sig.index.tolist()
peak_bed_sig = peak_bed[peak_bed['peak'].isin(sig_peaks)]
peak_bed_sig.to_csv("significant_peaks.bed", sep='\t', index=False, header=False)

# 然后运行HOMER：
# findMotifsGenome.pl significant_peaks.bed hg38 motif_output/ -size 200
```

## 第10步：基因活性评分

```python
# 从peak可及性计算基因活性
# （需要peak-基因注释）

def compute_gene_activity(adata, peak_gene_map):
    """
    从peak可及性计算基因活性评分。

    Parameters
    ----------
    adata : AnnData
        含peak的ATAC数据
    peak_gene_map : dict
        peak到基因的映射

    Returns
    -------
    含基因活性评分的AnnData
    """
    from scipy.sparse import csr_matrix

    genes = list(set(peak_gene_map.values()))
    gene_matrix = np.zeros((adata.n_obs, len(genes)))

    for i, gene in enumerate(genes):
        gene_peaks = [p for p, g in peak_gene_map.items() if g == gene]
        if gene_peaks:
            peak_idx = [list(adata.var_names).index(p) for p in gene_peaks if p in adata.var_names]
            if peak_idx:
                gene_matrix[:, i] = np.array(adata.X[:, peak_idx].sum(axis=1)).flatten()

    adata_gene = ad.AnnData(
        X=csr_matrix(gene_matrix),
        obs=adata.obs.copy(),
        var=pd.DataFrame(index=genes)
    )

    return adata_gene
```

## 完整流程

```python
def analyze_scatac(
    adata,
    batch_key=None,
    n_top_peaks=50000,
    n_latent=20,
    resolution=0.5
):
    """
    使用PeakVI进行完整的scATAC-seq分析。

    Parameters
    ----------
    adata : AnnData
        原始peak-cell矩阵
    batch_key : str, optional
        批次注释列
    n_top_peaks : int
        使用的顶部peak数量
    n_latent : int
        潜在维度
    resolution : float
        Leiden聚类分辨率

    Returns
    -------
    处理后的AnnData和训练好的模型的元组
    """
    import scvi
    import scanpy as sc
    import numpy as np

    adata = adata.copy()

    # QC
    sc.pp.calculate_qc_metrics(adata, inplace=True)
    adata = adata[adata.obs['n_genes_by_counts'] > 500].copy()
    sc.pp.filter_genes(adata, min_cells=10)

    # 二值化
    adata.X = (adata.X > 0).astype(np.float32)

    # 选择顶部peak
    if adata.n_vars > n_top_peaks:
        peak_accessibility = np.array(adata.X.sum(axis=0)).flatten()
        top_peaks = np.argsort(peak_accessibility)[-n_top_peaks:]
        adata = adata[:, top_peaks].copy()

    # 设置PeakVI
    scvi.model.PEAKVI.setup_anndata(adata, batch_key=batch_key)

    # 训练
    model = scvi.model.PEAKVI(adata, n_latent=n_latent)
    model.train(max_epochs=200, early_stopping=True)

    # 潜在表示
    adata.obsm["X_PeakVI"] = model.get_latent_representation()

    # 聚类
    sc.pp.neighbors(adata, use_rep="X_PeakVI")
    sc.tl.umap(adata)
    sc.tl.leiden(adata, resolution=resolution)

    return adata, model

# 用法
adata, model = analyze_scatac(
    adata,
    batch_key="sample",
    n_top_peaks=50000
)

# 可视化
sc.pl.umap(adata, color=['leiden', 'sample'])

# 差异可及性
da_results = model.differential_accessibility(
    groupby='leiden',
    group1='0',
    group2='1'
)
```

## 与scRNA-seq整合

对于多组学数据或来自同一细胞的单独RNA/ATAC：

```python
# 参见MultiVI用于联合RNA+ATAC分析
# 或使用WNN（加权最近邻）方法

# 使用共享潜在空间将标签从RNA转移到ATAC
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 训练缓慢 | peak太多 | 子集至前50k个peak |
| 聚类不佳 | 信息量peak太少 | 使用可变peak |
| 批次主导 | 技术效应很强 | 确保设置了batch_key |
| 内存错误 | peak矩阵太大 | 使用稀疏格式，减少peak |

## 关键参考文献

- Ashuach et al. (2022) "PeakVI: A deep generative model for single-cell chromatin accessibility analysis"
