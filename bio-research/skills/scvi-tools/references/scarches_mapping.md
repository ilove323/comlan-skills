# 使用scArches进行参考映射

本参考文档介绍使用scArches将查询数据映射到预训练参考模型，无需从头开始重训练。

## 概述

scArches（单细胞架构手术）支持：
- 将新数据映射到现有参考图谱
- 用新批次/研究扩展模型
- 无需完整重训练的迁移学习
- 在整合查询数据的同时保留参考结构

## 何时使用scArches

| 场景 | 方法 |
|------|------|
| 将查询数据映射到现有图谱 | scArches查询映射 |
| 用新数据扩展图谱 | scArches模型手术 |
| 无可用预训练模型 | 从头训练scANVI |
| 查询数据与参考差异很大 | 考虑重新训练 |

## 先决条件

```python
import scvi
import scanpy as sc
import numpy as np

print(f"scvi-tools version: {scvi.__version__}")
```

## 工作流程1：将查询数据映射到预训练参考模型

### 第1步：加载预训练参考模型

```python
# 加载保存的参考模型
# 该模型必须使用scvi-tools训练
reference_model = scvi.model.SCVI.load("reference_model/")

# 或加载scANVI进行标签转移
reference_model = scvi.model.SCANVI.load("reference_scanvi_model/")

# 检查模型信息
print(f"Model type: {type(reference_model)}")
print(f"Training data shape: {reference_model.adata.shape}")
```

### 第2步：准备查询数据

```python
# 加载查询数据
adata_query = sc.read_h5ad("query_data.h5ad")

# 关键：将基因与参考数据匹配
reference_genes = reference_model.adata.var_names
query_genes = adata_query.var_names

# 检查重叠
common_genes = reference_genes.intersection(query_genes)
print(f"Reference genes: {len(reference_genes)}")
print(f"Query genes: {len(query_genes)}")
print(f"Overlap: {len(common_genes)}")

# 将查询数据子集至参考基因
adata_query = adata_query[:, reference_genes].copy()

# 缺失基因由prepare_query_anndata自动填充为零
```

### 第3步：准备查询AnnData

```python
# 存储原始计数
adata_query.layers["counts"] = adata_query.X.copy()

# 为映射准备查询数据
# 这将查询数据结构对齐以匹配参考数据
scvi.model.SCVI.prepare_query_anndata(adata_query, reference_model)
```

### 第4步：创建查询模型

```python
# 从参考数据创建查询模型
# 使用参考权重初始化
query_model = scvi.model.SCVI.load_query_data(
    adata_query,
    reference_model
)

# 查询模型继承：
# - 参考架构
# - 参考编码器权重（默认冻结）
# - 解码器针对查询数据进行微调
```

### 第5步：在查询数据上微调

```python
# 微调查询模型
# 这将调整解码器权重以适应查询特异性效应
query_model.train(
    max_epochs=200,
    plan_kwargs={
        "weight_decay": 0.0  # 微调时减少正则化
    }
)

# 检查训练
query_model.history['elbo_train'].plot()
```

### 第6步：获取查询表示

```python
# 获取潜在表示
# 查询细胞嵌入到与参考数据相同的空间
adata_query.obsm["X_scVI"] = query_model.get_latent_representation()

# 可视化
sc.pp.neighbors(adata_query, use_rep="X_scVI")
sc.tl.umap(adata_query)
sc.pl.umap(adata_query, color=['cell_type', 'batch'])
```

## 工作流程2：含标签转移的scANVI查询映射

将细胞类型标签从参考数据转移到查询数据：

### 第1步：加载scANVI参考模型

```python
# 参考模型必须是scANVI模型（使用标签训练）
reference_scanvi = scvi.model.SCANVI.load("scanvi_reference/")

# 检查可用标签
print("Reference cell types:")
print(reference_scanvi.adata.obs['cell_type'].value_counts())
```

### 第2步：准备并映射查询数据

```python
# 准备查询数据
adata_query.layers["counts"] = adata_query.X.copy()
adata_query = adata_query[:, reference_scanvi.adata.var_names].copy()

scvi.model.SCANVI.prepare_query_anndata(adata_query, reference_scanvi)

# 创建查询模型
query_scanvi = scvi.model.SCANVI.load_query_data(
    adata_query,
    reference_scanvi
)

# 微调
query_scanvi.train(
    max_epochs=100,
    plan_kwargs={"weight_decay": 0.0}
)
```

### 第3步：获取预测

```python
# 预测细胞类型
predictions = query_scanvi.predict()
adata_query.obs["predicted_cell_type"] = predictions

# 获取预测概率
soft_predictions = query_scanvi.predict(soft=True)
adata_query.obs["prediction_confidence"] = soft_predictions.max(axis=1)

# 潜在表示
adata_query.obsm["X_scANVI"] = query_scanvi.get_latent_representation()

# 可视化预测
sc.pp.neighbors(adata_query, use_rep="X_scANVI")
sc.tl.umap(adata_query)
sc.pl.umap(adata_query, color=['predicted_cell_type', 'prediction_confidence'])
```

### 第4步：评估预测

```python
# 预测分布
print(adata_query.obs['predicted_cell_type'].value_counts())

# 置信度统计
print(f"Mean confidence: {adata_query.obs['prediction_confidence'].mean():.3f}")
print(f"Low confidence (<0.5): {(adata_query.obs['prediction_confidence'] < 0.5).sum()}")

# 过滤低置信度预测
high_conf = adata_query[adata_query.obs['prediction_confidence'] >= 0.7].copy()
print(f"High confidence cells: {len(high_conf)} ({len(high_conf)/len(adata_query)*100:.1f}%)")
```

## 工作流程3：模型手术（扩展参考模型）

用新数据扩展现有参考模型：

### 第1步：冻结参考层

```python
# 加载参考模型
reference_model = scvi.model.SCVI.load("reference_model/")

# 获取参考表示（手术前）
adata_ref = reference_model.adata
adata_ref.obsm["X_scVI_before"] = reference_model.get_latent_representation()
```

### 第2步：准备合并数据

```python
# 添加批次信息
adata_ref.obs["dataset"] = "reference"
adata_query.obs["dataset"] = "query"

# 合并
adata_combined = sc.concat([adata_ref, adata_query])
adata_combined.layers["counts"] = adata_combined.X.copy()
```

### 第3步：手术方法

```python
# 选项A：使用load_query_data（推荐）
scvi.model.SCVI.prepare_query_anndata(adata_query, reference_model)
extended_model = scvi.model.SCVI.load_query_data(adata_query, reference_model)
extended_model.train(max_epochs=200)

# 选项B：用合并数据重新训练（如果查询数据量大）
# 这不能完全保留参考数据，但可能产生更好的结果
scvi.model.SCVI.setup_anndata(
    adata_combined,
    layer="counts",
    batch_key="dataset"
)
new_model = scvi.model.SCVI(adata_combined, n_latent=30)
new_model.train(max_epochs=200)
```

## 联合可视化

联合可视化参考数据和查询数据：

```python
# 获取潜在表示
adata_ref.obsm["X_scVI"] = reference_model.get_latent_representation()
adata_query.obsm["X_scVI"] = query_model.get_latent_representation()

# 合并以进行可视化
adata_ref.obs["source"] = "reference"
adata_query.obs["source"] = "query"
adata_combined = sc.concat([adata_ref, adata_query])

# 计算联合UMAP
sc.pp.neighbors(adata_combined, use_rep="X_scVI")
sc.tl.umap(adata_combined)

# 可视化
import matplotlib.pyplot as plt
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

sc.pl.umap(adata_combined, color="source", ax=axes[0], show=False, title="Source")
sc.pl.umap(adata_combined, color="cell_type", ax=axes[1], show=False, title="Cell Type")
sc.pl.umap(adata_combined, color="batch", ax=axes[2], show=False, title="Batch")

plt.tight_layout()
```

## 使用公共图谱模型

### 来自HuggingFace模型中心

```python
from huggingface_hub import hf_hub_download

# 下载模型文件
model_dir = hf_hub_download(
    repo_id="scvi-tools/model-name",  # 替换为实际仓库
    filename="model.pt",
    local_dir="./downloaded_model/"
)

# 加载模型
atlas_model = scvi.model.SCANVI.load(model_dir)
```

### 来自CellxGene

```python
# 许多CellxGene数据集提供预训练模型
# 查看数据集文档了解模型可用性
# https://cellxgene.cziscience.com/

# 示例工作流程：
# 1. 下载参考数据集和模型
# 2. 加载模型：model = scvi.model.SCANVI.load("cellxgene_model/")
# 3. 使用上述步骤映射查询数据
```

## 完整流程

```python
def map_query_to_reference(
    adata_query,
    reference_model_path,
    model_type="scanvi",
    max_epochs=100,
    confidence_threshold=0.5
):
    """
    将查询数据映射到预训练参考模型。

    Parameters
    ----------
    adata_query : AnnData
        含原始计数的查询数据
    reference_model_path : str
        保存的参考模型路径
    model_type : str
        "scvi"或"scanvi"
    max_epochs : int
        微调epoch数
    confidence_threshold : float
        最低预测置信度（用于scANVI）

    Returns
    -------
    含预测的映射AnnData（如果使用scANVI）
    """
    import scvi

    # 加载参考模型
    if model_type == "scanvi":
        reference_model = scvi.model.SCANVI.load(reference_model_path)
        ModelClass = scvi.model.SCANVI
    else:
        reference_model = scvi.model.SCVI.load(reference_model_path)
        ModelClass = scvi.model.SCVI

    # 准备查询数据
    adata_query = adata_query.copy()
    adata_query = adata_query[:, reference_model.adata.var_names].copy()
    adata_query.layers["counts"] = adata_query.X.copy()

    # 映射查询数据
    ModelClass.prepare_query_anndata(adata_query, reference_model)
    query_model = ModelClass.load_query_data(adata_query, reference_model)

    # 微调
    query_model.train(
        max_epochs=max_epochs,
        plan_kwargs={"weight_decay": 0.0}
    )

    # 获取结果
    rep_key = "X_scANVI" if model_type == "scanvi" else "X_scVI"
    adata_query.obsm[rep_key] = query_model.get_latent_representation()

    if model_type == "scanvi":
        adata_query.obs["predicted_cell_type"] = query_model.predict()
        soft = query_model.predict(soft=True)
        adata_query.obs["prediction_confidence"] = soft.max(axis=1)
        adata_query.obs["confident"] = adata_query.obs["prediction_confidence"] >= confidence_threshold

    # 计算UMAP
    sc.pp.neighbors(adata_query, use_rep=rep_key)
    sc.tl.umap(adata_query)

    return adata_query, query_model


# 用法
adata_mapped, model = map_query_to_reference(
    adata_query,
    "reference_scanvi_model/",
    model_type="scanvi"
)

# 可视化
sc.pl.umap(adata_mapped, color=['predicted_cell_type', 'prediction_confidence'])
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 基因不匹配 | 基因命名不同 | 转换基因ID（Ensembl↔Symbol） |
| 低置信度较多 | 查询数据含新型细胞类型 | 手动注释低置信度细胞 |
| 映射效果差 | 查询数据差异过大 | 考虑用合并数据重新训练 |
| 内存错误 | 查询数据量大 | 分批处理 |
| 版本不匹配 | scvi-tools版本不同 | 使用与参考训练相同的版本 |

## 关键参考文献

- Lotfollahi et al. (2022) "Mapping single-cell data to reference atlases by transfer learning"
- Xu et al. (2021) "Probabilistic harmonization and annotation of single-cell transcriptomics data with deep generative models"
