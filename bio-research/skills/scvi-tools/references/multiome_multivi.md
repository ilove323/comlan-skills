# 使用MultiVI进行多组学分析

本参考文档介绍使用MultiVI对多组学实验（RNA+ATAC）进行联合分析。

## 概述

MultiVI是用于分析多组学数据（同一细胞的RNA-seq和ATAC-seq）的深度生成模型，它：
- 学习跨模态的联合潜在表示
- 处理缺失模态（仅RNA或仅ATAC的细胞）
- 支持跨实验的批次校正
- 支持缺失模态的插补

## 先决条件

```python
import scvi
import scanpy as sc
import mudata as md
import numpy as np

print(f"scvi-tools version: {scvi.__version__}")
```

## 数据格式

### 选项1：MuData（推荐）

```python
# 以MuData格式加载多组学数据
mdata = md.read("multiome.h5mu")

# 结构：
# mdata.mod['rna']  - 含RNA计数的AnnData
# mdata.mod['atac'] - 含ATAC计数的AnnData

print(f"RNA: {mdata.mod['rna'].shape}")
print(f"ATAC: {mdata.mod['atac'].shape}")
```

### 选项2：独立AnnData对象

```python
# 分别加载
adata_rna = sc.read_h5ad("rna.h5ad")
adata_atac = sc.read_h5ad("atac.h5ad")

# 确保相同细胞按相同顺序排列
common_cells = adata_rna.obs_names.intersection(adata_atac.obs_names)
adata_rna = adata_rna[common_cells].copy()
adata_atac = adata_atac[common_cells].copy()
```

## 第1步：准备RNA数据

```python
# RNA预处理（标准scvi-tools流程）
adata_rna = mdata.mod['rna'].copy()

# 过滤
sc.pp.filter_cells(adata_rna, min_genes=200)
sc.pp.filter_genes(adata_rna, min_cells=3)

# 存储计数
adata_rna.layers["counts"] = adata_rna.X.copy()

# HVG选择
sc.pp.highly_variable_genes(
    adata_rna,
    n_top_genes=4000,
    flavor="seurat_v3",
    layer="counts",
    batch_key="batch"  # 如有多批次
)

# 子集至HVG
adata_rna = adata_rna[:, adata_rna.var['highly_variable']].copy()
```

## 第2步：准备ATAC数据

```python
# ATAC预处理
adata_atac = mdata.mod['atac'].copy()

# 过滤peak
sc.pp.filter_genes(adata_atac, min_cells=10)

# 二值化可及性
adata_atac.X = (adata_atac.X > 0).astype(np.float32)

# 如果peak太多，选择最可及的peak
if adata_atac.n_vars > 50000:
    peak_accessibility = np.array(adata_atac.X.sum(axis=0)).flatten()
    top_peaks = np.argsort(peak_accessibility)[-50000:]
    adata_atac = adata_atac[:, top_peaks].copy()

# 存储到层中
adata_atac.layers["counts"] = adata_atac.X.copy()
```

## 第3步：创建合并的MuData

```python
# 确保细胞匹配
common_cells = adata_rna.obs_names.intersection(adata_atac.obs_names)
adata_rna = adata_rna[common_cells].copy()
adata_atac = adata_atac[common_cells].copy()

# 创建MuData
mdata = md.MuData({
    "rna": adata_rna,
    "atac": adata_atac
})

print(f"Combined multiome: {mdata.n_obs} cells")
print(f"RNA features: {mdata.mod['rna'].n_vars}")
print(f"ATAC features: {mdata.mod['atac'].n_vars}")
```

## 第4步：设置MultiVI

```python
# 为MultiVI设置MuData
scvi.model.MULTIVI.setup_mudata(
    mdata,
    rna_layer="counts",
    atac_layer="counts",
    batch_key="batch",  # 可选
    modalities={
        "rna_layer": "rna",
        "batch_key": "rna",
        "atac_layer": "atac"
    }
)
```

## 第5步：训练MultiVI

```python
# 创建模型
model = scvi.model.MULTIVI(
    mdata,
    n_latent=20,
    n_layers_encoder=2,
    n_layers_decoder=2
)

# 训练
model.train(
    max_epochs=300,
    early_stopping=True,
    early_stopping_patience=10,
    batch_size=128
)

# 检查训练
model.history['elbo_train'].plot()
```

## 第6步：获取联合表示

```python
# 潜在表示
latent = model.get_latent_representation()

# 添加到MuData
mdata.obsm["X_MultiVI"] = latent

# 在联合空间上聚类
sc.pp.neighbors(mdata, use_rep="X_MultiVI")
sc.tl.umap(mdata)
sc.tl.leiden(mdata, resolution=1.0)

# 可视化
sc.pl.umap(mdata, color=['leiden', 'batch'], ncols=2)
```

## 第7步：模态特异性分析

### 插补缺失模态

```python
# 为仅ATAC的细胞插补RNA表达
# （整合仅ATAC数据集时有用）
imputed_rna = model.get_normalized_expression(
    modality="rna"
)

# 为仅RNA的细胞插补可及性
imputed_atac = model.get_accessibility_estimates()
```

### 差异分析

```python
# 差异表达（RNA）
de_results = model.differential_expression(
    groupby="leiden",
    group1="0",
    group2="1"
)

# 差异可及性（ATAC）
da_results = model.differential_accessibility(
    groupby="leiden",
    group1="0",
    group2="1"
)
```

## 处理部分数据

MultiVI可以整合只有一种模态的数据集：

```python
# 数据集1：完整多组学
# 数据集2：仅RNA
# 数据集3：仅ATAC

# 标记缺失模态
mdata.obs['modality'] = 'paired'  # 同时具有两种模态的细胞
# 对于仅RNA的细胞，ATAC数据应为missing/NaN
# 对于仅ATAC的细胞，RNA数据应为missing/NaN

# MultiVI在训练期间自动处理这种情况
```

## 完整流程

```python
def analyze_multiome(
    adata_rna,
    adata_atac,
    batch_key=None,
    n_top_genes=4000,
    n_top_peaks=50000,
    n_latent=20,
    max_epochs=300
):
    """
    使用MultiVI进行完整的多组学分析。

    Parameters
    ----------
    adata_rna : AnnData
        RNA计数数据
    adata_atac : AnnData
        ATAC peak数据
    batch_key : str, optional
        批次列名
    n_top_genes : int
        RNA的HVG数量
    n_top_peaks : int
        ATAC的顶部peak数量
    n_latent : int
        潜在维度
    max_epochs : int
        最大训练epoch数

    Returns
    -------
    含联合表示的MuData
    """
    import scvi
    import scanpy as sc
    import mudata as md
    import numpy as np

    # 获取共同细胞
    common_cells = adata_rna.obs_names.intersection(adata_atac.obs_names)
    adata_rna = adata_rna[common_cells].copy()
    adata_atac = adata_atac[common_cells].copy()

    # RNA预处理
    sc.pp.filter_genes(adata_rna, min_cells=3)
    adata_rna.layers["counts"] = adata_rna.X.copy()

    if batch_key:
        sc.pp.highly_variable_genes(
            adata_rna, n_top_genes=n_top_genes,
            flavor="seurat_v3", layer="counts", batch_key=batch_key
        )
    else:
        sc.pp.normalize_total(adata_rna, target_sum=1e4)
        sc.pp.log1p(adata_rna)
        sc.pp.highly_variable_genes(adata_rna, n_top_genes=n_top_genes)
        adata_rna.X = adata_rna.layers["counts"].copy()

    adata_rna = adata_rna[:, adata_rna.var['highly_variable']].copy()

    # ATAC预处理
    sc.pp.filter_genes(adata_atac, min_cells=10)
    adata_atac.X = (adata_atac.X > 0).astype(np.float32)

    if adata_atac.n_vars > n_top_peaks:
        peak_acc = np.array(adata_atac.X.sum(axis=0)).flatten()
        top_idx = np.argsort(peak_acc)[-n_top_peaks:]
        adata_atac = adata_atac[:, top_idx].copy()

    adata_atac.layers["counts"] = adata_atac.X.copy()

    # 创建MuData
    mdata = md.MuData({"rna": adata_rna, "atac": adata_atac})

    # 设置并训练
    scvi.model.MULTIVI.setup_mudata(
        mdata,
        rna_layer="counts",
        atac_layer="counts",
        batch_key=batch_key,
        modalities={"rna_layer": "rna", "batch_key": "rna", "atac_layer": "atac"}
    )

    model = scvi.model.MULTIVI(mdata, n_latent=n_latent)
    model.train(max_epochs=max_epochs, early_stopping=True)

    # 添加表示
    mdata.obsm["X_MultiVI"] = model.get_latent_representation()

    # 聚类
    sc.pp.neighbors(mdata, use_rep="X_MultiVI")
    sc.tl.umap(mdata)
    sc.tl.leiden(mdata)

    return mdata, model


# 用法
mdata, model = analyze_multiome(
    adata_rna,
    adata_atac,
    batch_key="sample"
)

sc.pl.umap(mdata, color=['leiden', 'sample'])
```

## Peak-基因关联

```python
# 基于潜在空间中的相关性将ATAC peak与基因关联
# 这有助于识别调控关系

def link_peaks_to_genes(model, mdata, distance_threshold=100000):
    """
    基于相关性将peak与邻近基因关联。

    Parameters
    ----------
    model : MULTIVI
        训练好的模型
    mdata : MuData
        多组学数据
    distance_threshold : int
        关联peak与基因的最大距离（bp）

    Returns
    -------
    peak-基因关联的DataFrame
    """
    # 获取插补值
    rna_imputed = model.get_normalized_expression()
    atac_imputed = model.get_accessibility_estimates()

    # 将peak可及性与基因表达相关联
    # 针对基因启动子附近的peak
    # ...（需要基因组坐标）

    return peak_gene_links
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 细胞数不同 | 一种模态中缺少细胞 | 仅使用共同细胞 |
| 训练不稳定 | 模态不平衡 | 标准化特征计数 |
| 聚类效果差 | 特征太少 | 增加n_top_genes/peaks |
| 内存错误 | ATAC矩阵太大 | 减少peak数量，使用稀疏格式 |
| 批次主导 | 强烈的技术效应 | 确保设置了batch_key |

## 关键参考文献

- Ashuach et al. (2023) "MultiVI: deep generative model for the integration of multimodal data"
