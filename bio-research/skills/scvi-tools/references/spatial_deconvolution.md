# 空间转录组学分析

本参考文档介绍使用scvi-tools方法进行空间转录组学分析：DestVI用于去卷积，resolVI用于构建空间模型。

## 概述

Visium等空间转录组学技术在特定空间位置捕获基因表达，但许多平台具有多细胞分辨率。scvi-tools提供两种主要方法：

- **DestVI**：去卷积——利用单细胞参考数据估计每个spot的细胞类型比例
- **resolVI**：构建空间模型，学习考虑空间背景的基因表达模式

## scvi-tools中的可用方法

| 方法 | 描述 | 使用场景 |
|------|------|---------|
| **DestVI** | 变分推断去卷积 | 估计每个spot的细胞类型比例 |
| **resolVI** | 空间基因表达模型 | 学习空间感知表示 |
| **CondSCVI** | DestVI的参考模型 | DestVI工作流程所需 |

## 先决条件

```python
import scvi
import scanpy as sc
import squidpy as sq
import numpy as np

print(f"scvi-tools version: {scvi.__version__}")
```

---

## 第一部分：DestVI去卷积

### 第1步：加载空间数据

```python
# 加载Visium数据
adata_spatial = sc.read_visium("spaceranger_output/")

# 检查结构
print(f"Spots: {adata_spatial.n_obs}")
print(f"Genes: {adata_spatial.n_vars}")
print(f"Spatial coordinates: {adata_spatial.obsm['spatial'].shape}")

# 基本QC
sc.pp.calculate_qc_metrics(adata_spatial, inplace=True)
adata_spatial = adata_spatial[adata_spatial.obs['n_genes_by_counts'] > 200].copy()

# 存储计数
adata_spatial.layers["counts"] = adata_spatial.X.copy()
```

### 第2步：加载单细胞参考数据

```python
# 加载参考单细胞数据
adata_sc = sc.read_h5ad("reference_scrna.h5ad")

# 要求：
# - 原始计数
# - 细胞类型注释
print(f"Reference cells: {adata_sc.n_obs}")
print(f"Cell types: {adata_sc.obs['cell_type'].nunique()}")
print(adata_sc.obs['cell_type'].value_counts())

# 存储计数
adata_sc.layers["counts"] = adata_sc.X.copy()
```

### 第3步：准备数据

```python
# DestVI需要参考数据和空间数据之间的基因重叠
common_genes = adata_sc.var_names.intersection(adata_spatial.var_names)
print(f"Common genes: {len(common_genes)}")

adata_sc = adata_sc[:, common_genes].copy()
adata_spatial = adata_spatial[:, common_genes].copy()
```

### 第4步：训练参考模型（CondSCVI）

```python
# 在参考数据上训练条件scVI
scvi.model.CondSCVI.setup_anndata(
    adata_sc,
    layer="counts",
    labels_key="cell_type"
)

sc_model = scvi.model.CondSCVI(
    adata_sc,
    n_latent=20
)

sc_model.train(max_epochs=200)
sc_model.history['elbo_train'].plot()
```

### 第5步：训练DestVI

```python
# 设置空间数据
scvi.model.DestVI.setup_anndata(
    adata_spatial,
    layer="counts"
)

# 使用参考模型训练DestVI
spatial_model = scvi.model.DestVI.from_rna_model(
    adata_spatial,
    sc_model
)

spatial_model.train(max_epochs=500)
```

### 第6步：获取细胞类型比例

```python
# 推断每个spot的细胞类型比例
proportions = spatial_model.get_proportions()

# 添加到adata
for ct in adata_sc.obs['cell_type'].unique():
    adata_spatial.obs[f'prop_{ct}'] = proportions[ct]

# 可视化
sq.pl.spatial_scatter(
    adata_spatial,
    color=[f'prop_{ct}' for ct in adata_sc.obs['cell_type'].unique()[:6]],
    ncols=3
)
```

---

## 第二部分：resolVI空间模型

resolVI是一种半监督方法，直接从空间数据学习细胞类型分配和空间感知表示，可选择使用初始细胞类型预测。

**注意**：resolVI在`scvi.external`中（而非`scvi.model`）。

### 第1步：准备空间数据

```python
# 加载并预处理
adata = sc.read_visium("spaceranger_output/")

# QC
sc.pp.calculate_qc_metrics(adata, inplace=True)
adata = adata[adata.obs['n_genes_by_counts'] > 200].copy()

# 存储计数
adata.layers["counts"] = adata.X.copy()

# HVG选择
sc.pp.highly_variable_genes(
    adata,
    n_top_genes=4000,
    flavor="seurat_v3",
    layer="counts"
)
adata = adata[:, adata.var['highly_variable']].copy()

# 可选：获取初始细胞类型预测（例如来自参考数据）
# adata.obs["predicted_celltype"] = ...
```

### 第2步：设置并训练resolVI

```python
# 为resolVI设置（注意：scvi.external，而非scvi.model）
scvi.external.RESOLVI.setup_anndata(
    adata,
    labels_key="predicted_celltype",  # 初始细胞类型预测
    layer="counts"
)

# 创建模型（semisupervised=True使用标签）
model = scvi.external.RESOLVI(adata, semisupervised=True)

# 训练
model.train(max_epochs=50)
```

### 第3步：获取细胞类型预测

```python
# 获取精细化的细胞类型预测
# soft=True返回概率，soft=False返回标签
cell_type_probs = model.predict(adata, num_samples=3, soft=True)
cell_type_labels = model.predict(adata, num_samples=3, soft=False)

adata.obs["resolvi_celltype"] = cell_type_labels

# 可视化
sq.pl.spatial_scatter(adata, color="resolvi_celltype")
```

### 第4步：获取潜在表示

```python
# 获取潜在表示
adata.obsm["X_resolVI"] = model.get_latent_representation(adata)

# 基于空间表示聚类
sc.pp.neighbors(adata, use_rep="X_resolVI")
sc.tl.umap(adata)
sc.tl.leiden(adata, resolution=0.5)

# 空间可视化聚类
sq.pl.spatial_scatter(adata, color="leiden")
```

### 第5步：差异表达

```python
# 使用resolVI进行细胞类型间的差异表达
de_results = model.differential_expression(
    adata,
    groupby="resolvi_celltype",
    group1="T_cell",
    group2="Tumor"
)

print(de_results.head(20))
```

### 第6步：生态位丰度分析

```python
# 分析细胞类型邻域在条件间的差异
# 需要空间邻居图
sq.gr.spatial_neighbors(adata, coord_type="generic")

niche_results = model.differential_niche_abundance(
    groupby="resolvi_celltype",
    group1="T_cell",
    group2="Tumor",
    neighbor_key="spatial_neighbors"
)
```

### 第7步：查询映射（转移到新数据）

```python
# 将新空间数据映射到已训练的模型
query_adata = sc.read_visium("new_sample/")
query_adata.layers["counts"] = query_adata.X.copy()

# 准备并加载查询数据
model.prepare_query_anndata(query_adata, reference_model=model)
query_model = model.load_query_data(query_adata, reference_model=model)

# 在查询数据上微调
query_model.train(max_epochs=20)

# 获取查询数据的预测
query_labels = query_model.predict(query_adata, num_samples=3, soft=False)
```

---

## 可视化

### 空间比例图

```python
import matplotlib.pyplot as plt

# 绘制多种细胞类型比例
cell_types = ['T_cell', 'Tumor', 'Fibroblast', 'Macrophage']
fig, axes = plt.subplots(2, 2, figsize=(12, 12))

for ax, ct in zip(axes.flat, cell_types):
    sq.pl.spatial_scatter(
        adata_spatial,
        color=f'prop_{ct}',
        ax=ax,
        title=ct,
        show=False
    )

plt.tight_layout()
```

### 按区域的富集

```python
# 对空间数据聚类
sc.pp.neighbors(adata_spatial)
sc.tl.leiden(adata_spatial, resolution=0.5)

# 比较各区域间的比例
import pandas as pd

cell_types = adata_sc.obs['cell_type'].unique()
prop_cols = [f'prop_{ct}' for ct in cell_types]
region_props = adata_spatial.obs.groupby('leiden')[prop_cols].mean()
print(region_props)

# 热图
import seaborn as sns
plt.figure(figsize=(10, 6))
sns.heatmap(region_props.T, annot=True, cmap='viridis')
plt.title('Cell Type Proportions by Region')
```

### 空间细胞类型相互作用

```python
# 使用细胞类型分配进行邻域富集
sq.gr.spatial_neighbors(adata_spatial)

# 创建"主导细胞类型"注释
prop_cols = [f'prop_{ct}' for ct in cell_types]
adata_spatial.obs['dominant_type'] = adata_spatial.obs[prop_cols].idxmax(axis=1)
adata_spatial.obs['dominant_type'] = adata_spatial.obs['dominant_type'].str.replace('prop_', '')

# 共现分析
sq.gr.co_occurrence(adata_spatial, cluster_key='dominant_type')
sq.pl.co_occurrence(adata_spatial, cluster_key='dominant_type')
```

---

## DestVI完整流程

```python
def deconvolve_spatial(
    adata_spatial,
    adata_ref,
    cell_type_key="cell_type",
    n_latent=20,
    max_epochs_ref=200,
    max_epochs_spatial=500
):
    """
    使用DestVI进行空间去卷积。

    Parameters
    ----------
    adata_spatial : AnnData
        空间转录组学数据
    adata_ref : AnnData
        含细胞类型注释的单细胞参考数据
    cell_type_key : str
        adata_ref.obs中含细胞类型标签的列
    n_latent : int
        潜在维度
    max_epochs_ref : int
        参考模型的训练epoch数
    max_epochs_spatial : int
        空间模型的训练epoch数

    Returns
    -------
    obs中含细胞类型比例的AnnData
    """
    import scvi

    # 获取共同基因
    common_genes = adata_ref.var_names.intersection(adata_spatial.var_names)
    adata_ref = adata_ref[:, common_genes].copy()
    adata_spatial = adata_spatial[:, common_genes].copy()

    # 确保存储了计数
    if "counts" not in adata_ref.layers:
        adata_ref.layers["counts"] = adata_ref.X.copy()
    if "counts" not in adata_spatial.layers:
        adata_spatial.layers["counts"] = adata_spatial.X.copy()

    # 训练参考模型
    scvi.model.CondSCVI.setup_anndata(
        adata_ref,
        layer="counts",
        labels_key=cell_type_key
    )

    ref_model = scvi.model.CondSCVI(adata_ref, n_latent=n_latent)
    ref_model.train(max_epochs=max_epochs_ref)

    # 训练空间模型
    scvi.model.DestVI.setup_anndata(adata_spatial, layer="counts")

    spatial_model = scvi.model.DestVI.from_rna_model(
        adata_spatial,
        ref_model
    )
    spatial_model.train(max_epochs=max_epochs_spatial)

    # 获取比例
    proportions = spatial_model.get_proportions()

    cell_types = adata_ref.obs[cell_type_key].unique()
    for ct in cell_types:
        adata_spatial.obs[f'prop_{ct}'] = proportions[ct]

    # 添加主导类型
    prop_cols = [f'prop_{ct}' for ct in cell_types]
    adata_spatial.obs['dominant_type'] = adata_spatial.obs[prop_cols].idxmax(axis=1)
    adata_spatial.obs['dominant_type'] = adata_spatial.obs['dominant_type'].str.replace('prop_', '')

    return adata_spatial, ref_model, spatial_model

# 用法
adata_spatial, ref_model, spatial_model = deconvolve_spatial(
    adata_spatial,
    adata_sc,
    cell_type_key="cell_type"
)

# 可视化
sq.pl.spatial_scatter(
    adata_spatial,
    color=['dominant_type', 'prop_T_cell', 'prop_Tumor'],
    ncols=3
)
```

---

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 共同基因太少 | 基因命名不同 | 转换基因名（Ensembl↔Symbol） |
| 去卷积效果差 | 参考数据不匹配 | 使用与组织匹配的参考 |
| 所有spot同一类型 | 过度平滑 | 调整模型参数，检查参考多样性 |
| 比例值为NaN | 参考中缺少细胞类型 | 确保参考中包含所有预期类型 |
| 训练缓慢 | 空间数据集太大 | 减少max_epochs，增大batch_size |

## 关键参考文献

- Lopez et al. (2022) "DestVI identifies continuums of cell types in spatial transcriptomics data"
- [scvi-tools空间教程](https://docs.scvi-tools.org/en/stable/tutorials/index.html)
