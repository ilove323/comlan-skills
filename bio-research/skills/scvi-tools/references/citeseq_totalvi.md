# 使用totalVI进行CITE-seq分析

本参考文档介绍使用totalVI对CITE-seq数据（RNA+表面蛋白）进行多模态分析。

## 概述

CITE-seq结合：
- scRNA-seq（转录组）
- 蛋白质表面标志物（抗体衍生标签，ADT）

totalVI对两种模态进行联合建模，以：
- 跨批次整合
- 对蛋白质信号去噪
- 学习联合潜在表示
- 支持跨模态插补

## 先决条件

```python
import scvi
import scanpy as sc
import mudata as md
import numpy as np
import pandas as pd

print(f"scvi-tools version: {scvi.__version__}")
```

## 第1步：加载CITE-seq数据

### 来自10x Genomics（Cell Ranger）

```python
# 10x输出单独的基因表达和特征条形码
adata_rna = sc.read_10x_h5("filtered_feature_bc_matrix.h5", gex_only=False)

# 分离RNA和蛋白质
adata_protein = adata_rna[:, adata_rna.var['feature_types'] == 'Antibody Capture'].copy()
adata_rna = adata_rna[:, adata_rna.var['feature_types'] == 'Gene Expression'].copy()

print(f"RNA: {adata_rna.shape}")
print(f"Protein: {adata_protein.shape}")
```

### 来自MuData

```python
# 如果数据是MuData格式
mdata = md.read_h5mu("cite_seq.h5mu")

adata_rna = mdata['rna'].copy()
adata_protein = mdata['protein'].copy()
```

### 合并为单个AnnData

```python
# totalVI期望蛋白质数据在obsm中
adata = adata_rna.copy()

# 将蛋白质表达添加到obsm
adata.obsm["protein_expression"] = adata_protein.X.toarray() if hasattr(adata_protein.X, 'toarray') else adata_protein.X

# 存储蛋白质名称
adata.uns["protein_names"] = list(adata_protein.var_names)
```

## 第2步：质量控制

### RNA QC

```python
# 标准RNA QC
# 处理人类（MT-）和小鼠（mt-, Mt-）线粒体基因
    adata.var['mt'] = (
        adata.var_names.str.startswith('MT-') |
        adata.var_names.str.startswith('mt-') |
        adata.var_names.str.startswith('Mt-')
    )
sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], inplace=True)

# 过滤细胞
adata = adata[adata.obs['n_genes_by_counts'] > 200].copy()
adata = adata[adata.obs['pct_counts_mt'] < 20].copy()

# 过滤基因
sc.pp.filter_genes(adata, min_cells=3)
```

### 蛋白质QC

```python
# 蛋白质QC
protein_counts = adata.obsm["protein_expression"]
print(f"Protein counts per cell: min={protein_counts.sum(1).min():.0f}, max={protein_counts.sum(1).max():.0f}")

# 检查同型对照
# 同型对照应该计数较低
protein_names = adata.uns["protein_names"]
for i, name in enumerate(protein_names):
    if 'isotype' in name.lower() or 'control' in name.lower():
        print(f"{name}: mean={protein_counts[:, i].mean():.1f}")
```

## 第3步：数据准备

### 存储原始计数

```python
# 存储RNA计数
adata.layers["counts"] = adata.X.copy()

# 蛋白质必须是原始ADT计数（非CLR标准化）
# 警告：如果从Seurat导入，确保使用原始计数，而非CLR标准化数据
# Seurat的NormalizeData(normalization.method = "CLR")会转换计数——使用原始assay
```

### RNA的HVG选择

```python
# 为RNA选择HVG
# 注意：totalVI使用所有蛋白质，无论HVG如何

sc.pp.highly_variable_genes(
    adata,
    n_top_genes=4000,  # CITE-seq使用更多
    flavor="seurat_v3",
    batch_key="batch" if "batch" in adata.obs else None,
    layer="counts"
)

# 子集至HVG
adata = adata[:, adata.var["highly_variable"]].copy()
```

## 第4步：设置并训练totalVI

```python
# 为totalVI设置AnnData
scvi.model.TOTALVI.setup_anndata(
    adata,
    layer="counts",
    protein_expression_obsm_key="protein_expression",
    batch_key="batch"  # 可选
)

# 创建模型
model = scvi.model.TOTALVI(
    adata,
    n_latent=20,
    latent_distribution="normal"  # 或"ln"用于对数正态
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

## 第5步：获取潜在表示

```python
# 联合潜在空间
adata.obsm["X_totalVI"] = model.get_latent_representation()

# 聚类和可视化
sc.pp.neighbors(adata, use_rep="X_totalVI")
sc.tl.umap(adata)
sc.tl.leiden(adata, resolution=1.0)

sc.pl.umap(adata, color=['leiden', 'batch'])
```

## 第6步：去噪的蛋白质表达

```python
# 获取去噪的蛋白质值
# 这去除了蛋白质测量中的背景噪声

_, protein_denoised = model.get_normalized_expression(
    return_mean=True,
    transform_batch="batch1"  # 可选：标准化到特定批次
)

# 添加到adata
adata.obsm["protein_denoised"] = protein_denoised

# 可视化去噪的蛋白质
protein_names = adata.uns["protein_names"]
for i, protein in enumerate(protein_names[:5]):
    adata.obs[f"denoised_{protein}"] = protein_denoised[:, i]

sc.pl.umap(adata, color=[f"denoised_{p}" for p in protein_names[:5]])
```

## 第7步：标准化的RNA表达

```python
# 获取标准化的RNA表达
rna_normalized, _ = model.get_normalized_expression(
    return_mean=True
)

# 存储
adata.layers["totalVI_normalized"] = rna_normalized
```

## 第8步：差异表达

### RNA差异表达

```python
# 聚类间差异表达
de_rna = model.differential_expression(
    groupby="leiden",
    group1="0",
    group2="1"
)

# 过滤显著基因
de_sig = de_rna[
    (de_rna['is_de_fdr_0.05']) &
    (abs(de_rna['lfc_mean']) > 1)
]

print(f"Significant DE genes: {len(de_sig)}")
```

### 蛋白质差异表达

```python
# 蛋白质差异表达
de_protein = model.differential_expression(
    groupby="leiden",
    group1="0",
    group2="1",
    mode="protein"
)

print(de_protein.head(20))
```

## 第9步：可视化

### UMAP上的蛋白质表达

```python
# UMAP上的去噪蛋白质
import matplotlib.pyplot as plt

proteins_to_plot = ["CD3", "CD4", "CD8", "CD19", "CD14"]

fig, axes = plt.subplots(1, len(proteins_to_plot), figsize=(4*len(proteins_to_plot), 4))
for ax, protein in zip(axes, proteins_to_plot):
    idx = adata.uns["protein_names"].index(protein)
    sc.pl.umap(
        adata,
        color=adata.obsm["protein_denoised"][:, idx],
        ax=ax,
        title=protein,
        show=False
    )
plt.tight_layout()
```

### 联合热图

```python
# 每个聚类顶部基因和蛋白质的热图
sc.pl.dotplot(
    adata,
    var_names=de_sig.index[:20].tolist(),
    groupby="leiden",
    layer="totalVI_normalized"
)
```

## 第10步：细胞类型注释

```python
# 使用RNA和蛋白质标志物进行注释

# RNA标志物
rna_markers = {
    'T cells': ['CD3D', 'CD3E'],
    'CD4 T': ['CD4'],
    'CD8 T': ['CD8A', 'CD8B'],
    'B cells': ['CD19', 'MS4A1'],
    'Monocytes': ['CD14', 'LYZ']
}

# 检查去噪的蛋白质表达
for i, protein in enumerate(adata.uns["protein_names"]):
    if any(m in protein for m in ['CD3', 'CD4', 'CD8', 'CD19', 'CD14']):
        print(f"{protein}: cluster means")
        for cluster in adata.obs['leiden'].unique():
            mask = adata.obs['leiden'] == cluster
            mean_expr = adata.obsm["protein_denoised"][mask, i].mean()
            print(f"  Cluster {cluster}: {mean_expr:.2f}")
```

## 完整流程

```python
def analyze_citeseq(
    adata_rna,
    adata_protein,
    batch_key=None,
    n_top_genes=4000,
    n_latent=20
):
    """
    使用totalVI进行完整的CITE-seq分析。

    Parameters
    ----------
    adata_rna : AnnData
        RNA表达（原始计数）
    adata_protein : AnnData
        蛋白质表达（原始计数）
    batch_key : str, optional
        obs中的批次列
    n_top_genes : int
        HVG数量
    n_latent : int
        潜在维度

    Returns
    -------
    处理后的AnnData和训练好的模型的元组
    """
    import scvi
    import scanpy as sc

    # 确保相同的细胞
    common_cells = adata_rna.obs_names.intersection(adata_protein.obs_names)
    adata = adata_rna[common_cells].copy()
    adata_protein = adata_protein[common_cells].copy()

    # 将蛋白质添加到obsm
    adata.obsm["protein_expression"] = adata_protein.X.toarray() if hasattr(adata_protein.X, 'toarray') else adata_protein.X
    adata.uns["protein_names"] = list(adata_protein.var_names)

    # RNA QC
    # 处理人类（MT-）和小鼠（mt-, Mt-）线粒体基因
    adata.var['mt'] = (
        adata.var_names.str.startswith('MT-') |
        adata.var_names.str.startswith('mt-') |
        adata.var_names.str.startswith('Mt-')
    )
    sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], inplace=True)
    adata = adata[adata.obs['pct_counts_mt'] < 20].copy()
    sc.pp.filter_genes(adata, min_cells=3)

    # 存储计数
    adata.layers["counts"] = adata.X.copy()

    # HVG选择
    sc.pp.highly_variable_genes(
        adata,
        n_top_genes=n_top_genes,
        flavor="seurat_v3",
        batch_key=batch_key,
        layer="counts"
    )
    adata = adata[:, adata.var["highly_variable"]].copy()

    # 设置totalVI
    scvi.model.TOTALVI.setup_anndata(
        adata,
        layer="counts",
        protein_expression_obsm_key="protein_expression",
        batch_key=batch_key
    )

    # 训练
    model = scvi.model.TOTALVI(adata, n_latent=n_latent)
    model.train(max_epochs=200, early_stopping=True)

    # 获取表示
    adata.obsm["X_totalVI"] = model.get_latent_representation()
    rna_norm, protein_denoised = model.get_normalized_expression(return_mean=True)
    adata.layers["totalVI_normalized"] = rna_norm
    adata.obsm["protein_denoised"] = protein_denoised

    # 聚类
    sc.pp.neighbors(adata, use_rep="X_totalVI")
    sc.tl.umap(adata)
    sc.tl.leiden(adata)

    return adata, model

# 用法
adata, model = analyze_citeseq(
    adata_rna,
    adata_protein,
    batch_key="batch"
)

# 可视化
sc.pl.umap(adata, color=['leiden', 'batch'])
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 蛋白质信号嘈杂 | 未去除背景 | 使用get_normalized_expression进行去噪 |
| 批次效应持续 | 需要batch_key | 确保指定了batch_key |
| 内存错误 | 基因太多 | 减少n_top_genes |
| 蛋白质聚类不佳 | 蛋白质太少 | 正常——totalVI使用RNA用于结构 |

## 关键参考文献

- Gayoso et al. (2021) "Joint probabilistic modeling of single-cell multi-omic data with totalVI"
