# scvi-tools数据准备

本参考文档介绍如何正确准备用于scvi-tools模型的AnnData对象。

## 概述

scvi-tools的数据准备至关重要。关键要求：
1. **原始计数**（非标准化）
2. **高变基因选择**
3. **正确的setup_anndata()调用**

## 第1步：加载并检查数据

```python
import scanpy as sc
import scvi
import numpy as np

# 加载数据
adata = sc.read_h5ad("data.h5ad")

# 检查adata.X的内容
print(f"Shape: {adata.shape}")
print(f"X dtype: {adata.X.dtype}")
print(f"X contains integers: {np.allclose(adata.X.data, adata.X.data.astype(int))}")
print(f"X min: {adata.X.min()}, max: {adata.X.max()}")
```

### 验证原始计数

```python
# scvi-tools需要整数计数
# 如果X看起来已标准化，检查原始计数

if hasattr(adata, 'raw') and adata.raw is not None:
    print("Found adata.raw")
    # 使用原始计数
    adata = adata.raw.to_adata()

# 或检查层
if 'counts' in adata.layers:
    print("Found counts layer")
    # 将在setup_anndata中指定层
```

## 第2步：基本过滤

```python
# 过滤细胞（标准QC）
sc.pp.filter_cells(adata, min_genes=200)
sc.pp.filter_cells(adata, max_genes=5000)

# 如果尚未计算，计算线粒体比例
# 处理人类（MT-）和小鼠（mt-, Mt-）线粒体基因
adata.var['mt'] = (
    adata.var_names.str.startswith('MT-') |
    adata.var_names.str.startswith('mt-') |
    adata.var_names.str.startswith('Mt-')
)
sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], inplace=True)
adata = adata[adata.obs['pct_counts_mt'] < 20].copy()

# 过滤基因
sc.pp.filter_genes(adata, min_cells=3)

print(f"After filtering: {adata.shape}")
```

## 第3步：存储原始计数

**关键**：在任何标准化之前始终保留原始计数。

```python
# 将原始计数存储在层中
adata.layers["counts"] = adata.X.copy()

# 现在可以为其他目的进行标准化（HVG选择）
# 但scvi将使用counts层
```

## 第4步：高变基因选择

scvi-tools在1,500-5,000个HVG时效果最佳。

### 对于单批次数据

```python
# 仅为HVG选择进行标准化
adata_hvg = adata.copy()
sc.pp.normalize_total(adata_hvg, target_sum=1e4)
sc.pp.log1p(adata_hvg)

# 选择HVG
sc.pp.highly_variable_genes(
    adata_hvg,
    n_top_genes=2000,
    flavor="seurat"  # 或"cell_ranger"
)

# 转移HVG注释
adata.var['highly_variable'] = adata_hvg.var['highly_variable']
```

### 对于多批次数据（推荐）

```python
# 使用seurat_v3 flavor和batch_key
# 这会选择在批次间变异的基因
sc.pp.highly_variable_genes(
    adata,
    n_top_genes=2000,
    flavor="seurat_v3",
    batch_key="batch",  # 你的批次列
    layer="counts"      # 使用原始计数
)
```

### 子集至HVG

```python
# 子集至高变基因
adata = adata[:, adata.var['highly_variable']].copy()
print(f"After HVG selection: {adata.shape}")
```

## 第5步：设置AnnData

`setup_anndata()`函数将数据注册给模型。

### 基本设置

```python
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts"  # 指定包含原始计数的层
)
```

### 含批次信息

```python
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch"  # adata.obs中的列
)
```

### 含细胞类型标签（用于scANVI）

```python
scvi.model.SCANVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch",
    labels_key="cell_type"  # 含细胞类型标签的列
)
```

### 含连续协变量

```python
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch",
    continuous_covariate_keys=["percent_mito", "n_genes"]
)
```

### 含分类协变量

```python
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch",
    categorical_covariate_keys=["donor", "technology"]
)
```

## 多模态数据设置

### CITE-seq（用于totalVI）

```python
# 蛋白质数据在adata.obsm中
# RNA在adata.X，蛋白质在单独矩阵中

# 添加蛋白质数据
adata.obsm["protein_expression"] = protein_counts  # numpy数组

# 为totalVI设置
scvi.model.TOTALVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch",
    protein_expression_obsm_key="protein_expression"
)
```

### 多组学RNA+ATAC（用于MultiVI）

```python
# RNA和ATAC在单独的AnnData对象或MuData中

import mudata as md

# 如果使用MuData
mdata = md.read("multiome.h5mu")

scvi.model.MULTIVI.setup_mudata(
    mdata,
    rna_layer="counts",
    protein_layer=None,
    batch_key="batch",
    modalities={"rna": "rna", "accessibility": "atac"}
)
```

## 完整准备流程

使用`scripts/model_utils.py`中的`prepare_adata()`函数进行完整准备：

```python
from model_utils import prepare_adata

# 准备数据，包含QC、HVG选择和层设置
adata = prepare_adata(
    adata,
    batch_key="batch",
    n_top_genes=2000,
    min_genes=200,
    max_mito_pct=20
)

# 然后为你的模型进行设置
import scvi
scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key="batch")
```

此函数处理：
- 线粒体QC过滤
- 细胞和基因过滤
- 将计数存储到层中
- HVG选择（如果提供batch_key，则批次感知）
- 子集至HVG

## 检查设置

```python
# 查看已注册的数据
print(adata.uns['_scvi_manager_uuid'])
print(adata.uns['_scvi_adata_minify_type'])

# 对于scVI
scvi.model.SCVI.view_anndata_setup(adata)
```

## 常见问题与解决方案

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| "X should contain integers" | X中有标准化数据 | 使用layer="counts" |
| "batch_key not found" | 列名错误 | 检查adata.obs.columns |
| 稀疏矩阵错误 | 不兼容格式 | 转换：adata.X = adata.X.toarray() |
| 内存错误 | 基因太多 | 先子集至HVG |
| 数据中有NaN | 缺失值 | 过滤或插补 |

## 数据格式参考

### 必需

- `adata.X`或`adata.layers["counts"]`：原始整数计数（稀疏格式可以）
- `adata.obs`：细胞元数据DataFrame
- `adata.var`：基因元数据DataFrame

### 推荐

- `adata.obs["batch"]`：批次/样本标识符
- `adata.var["highly_variable"]`：HVG布尔掩码

### 对于scANVI

- `adata.obs["labels"]`：细胞类型注释
- 可以包含"Unknown"用于未标注的细胞
