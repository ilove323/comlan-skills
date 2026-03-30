# 使用scANVI进行标签转移和参考映射

本参考文档介绍使用scANVI将细胞类型注释从参考图谱转移到查询数据。

## 概述

参考映射（也称为"标签转移"）使用在注释参考数据上预训练的模型来预测新的未注释查询数据中的细胞类型。这比重新聚类更快，并且跨研究具有更好的一致性。

scANVI在这方面表现出色，因为它：
- 在共享空间中联合嵌入参考数据和查询数据
- 以概率方式转移标签
- 处理参考数据和查询数据之间的批次效应

## 何时使用参考映射

- 使用现有图谱注释新数据集
- 跨多项研究保持一致的注释
- 速度快：无需重新聚类和手动注释
- 质量高：利用专家整理的参考注释

## 工作流程选项

1. **训练新模型**：在参考数据上训练scANVI，然后映射查询数据
2. **使用预训练模型**：加载现有模型（例如来自模型中心）
3. **scArches**：用查询数据扩展现有模型（保留参考数据）

## 选项1：在参考数据上训练scANVI

### 第1步：准备参考数据

```python
import scvi
import scanpy as sc

# 加载参考图谱
adata_ref = sc.read_h5ad("reference_atlas.h5ad")

# 检查注释
print(f"Reference cells: {adata_ref.n_obs}")
print(f"Cell types: {adata_ref.obs['cell_type'].nunique()}")
print(adata_ref.obs['cell_type'].value_counts())

# 确保是原始计数
adata_ref.layers["counts"] = adata_ref.raw.X.copy() if adata_ref.raw else adata_ref.X.copy()

# HVG选择
sc.pp.highly_variable_genes(
    adata_ref,
    n_top_genes=3000,
    flavor="seurat_v3",
    batch_key="batch" if "batch" in adata_ref.obs else None,
    layer="counts"
)
adata_ref = adata_ref[:, adata_ref.var["highly_variable"]].copy()
```

### 第2步：在参考数据上训练scANVI

```python
# 首先训练scVI（无标签）
scvi.model.SCVI.setup_anndata(
    adata_ref,
    layer="counts",
    batch_key="batch"
)

scvi_ref = scvi.model.SCVI(adata_ref, n_latent=30)
scvi_ref.train(max_epochs=200)

# 从scVI初始化scANVI
scanvi_ref = scvi.model.SCANVI.from_scvi_model(
    scvi_ref,
    labels_key="cell_type",
    unlabeled_category="Unknown"
)

# 训练scANVI
scanvi_ref.train(max_epochs=50)

# 保存以备后用
scanvi_ref.save("scanvi_reference_model/")
```

### 第3步：准备查询数据

```python
# 加载查询数据
adata_query = sc.read_h5ad("query_data.h5ad")

# 关键：使用与参考数据相同的基因
common_genes = adata_ref.var_names.intersection(adata_query.var_names)
print(f"Common genes: {len(common_genes)}")

# 将查询数据子集至参考基因
adata_query = adata_query[:, adata_ref.var_names].copy()

# 处理缺失基因（设为0）
missing_genes = set(adata_ref.var_names) - set(adata_query.var_names)
if missing_genes:
    # 添加计数为零的缺失基因
    import numpy as np
    from scipy.sparse import csr_matrix

    zero_matrix = csr_matrix((adata_query.n_obs, len(missing_genes)))
    # ... 拼接并重新排序以匹配参考数据

# 存储计数
adata_query.layers["counts"] = adata_query.X.copy()
```

### 第4步：将查询数据映射到参考数据

```python
# 为映射准备查询数据
scvi.model.SCANVI.prepare_query_anndata(adata_query, scanvi_ref)

# 从参考数据创建查询模型
scanvi_query = scvi.model.SCANVI.load_query_data(
    adata_query,
    scanvi_ref
)

# 在查询数据上微调（可选但推荐）
scanvi_query.train(
    max_epochs=100,
    plan_kwargs={"weight_decay": 0.0}
)

# 获取预测
adata_query.obs["predicted_cell_type"] = scanvi_query.predict()

# 获取预测概率
soft_predictions = scanvi_query.predict(soft=True)
adata_query.obs["prediction_score"] = soft_predictions.max(axis=1)
```

### 第5步：评估预测

```python
# 置信度评分
print(f"Mean prediction confidence: {adata_query.obs['prediction_score'].mean():.3f}")

# 低置信度预测
low_conf = adata_query.obs['prediction_score'] < 0.5
print(f"Low confidence cells: {low_conf.sum()} ({low_conf.mean()*100:.1f}%)")

# 可视化
sc.pp.neighbors(adata_query, use_rep="X_scANVI")
sc.tl.umap(adata_query)
sc.pl.umap(adata_query, color=['predicted_cell_type', 'prediction_score'])
```

## 选项2：使用预训练模型

### 来自模型中心

```python
# scvi-tools在HuggingFace上维护模型
# 查看：https://huggingface.co/scvi-tools

# 示例：加载预训练模型
from huggingface_hub import hf_hub_download

model_path = hf_hub_download(
    repo_id="scvi-tools/example-model",
    filename="model.pt"
)

# 加载模型
model = scvi.model.SCANVI.load(model_path, adata=adata_query)
```

### 来自已发表的图谱

```python
# 许多图谱提供预训练模型
# 使用CellTypist风格模型的示例工作流程

# 下载参考模型
# model = scvi.model.SCANVI.load("atlas_model/", adata=adata_query)
```

## 选项3：使用scArches进行增量更新

scArches无需从头开始重训练即可扩展参考模型：

```python
# 加载现有参考模型
scanvi_ref = scvi.model.SCANVI.load("reference_model/")

# 手术操作：为查询整合做准备
scanvi_ref.freeze_layers()

# 映射查询数据
scvi.model.SCANVI.prepare_query_anndata(adata_query, scanvi_ref)
scanvi_query = scvi.model.SCANVI.load_query_data(adata_query, scanvi_ref)

# 仅训练查询特异性参数
scanvi_query.train(
    max_epochs=200,
    plan_kwargs={"weight_decay": 0.0}
)
```

## 联合可视化参考数据和查询数据

```python
# 拼接以进行联合可视化
adata_ref.obs["dataset"] = "reference"
adata_query.obs["dataset"] = "query"

# 获取潜在表示
adata_ref.obsm["X_scANVI"] = scanvi_ref.get_latent_representation()
adata_query.obsm["X_scANVI"] = scanvi_query.get_latent_representation()

# 合并
adata_combined = sc.concat([adata_ref, adata_query])

# 计算合并的UMAP
sc.pp.neighbors(adata_combined, use_rep="X_scANVI")
sc.tl.umap(adata_combined)

# 绘图
sc.pl.umap(
    adata_combined,
    color=["dataset", "cell_type", "predicted_cell_type"],
    ncols=2
)
```

## 预测质量控制

### 置信度过滤

```python
# 按置信度过滤预测
confidence_threshold = 0.7

high_conf = adata_query[adata_query.obs['prediction_score'] >= confidence_threshold].copy()
low_conf = adata_query[adata_query.obs['prediction_score'] < confidence_threshold].copy()

print(f"High confidence: {len(high_conf)} ({len(high_conf)/len(adata_query)*100:.1f}%)")
print(f"Low confidence: {len(low_conf)} ({len(low_conf)/len(adata_query)*100:.1f}%)")
```

### 标志物验证

```python
# 用已知标志物验证预测
markers = {
    'T cells': ['CD3D', 'CD3E'],
    'B cells': ['CD19', 'MS4A1'],
    'Monocytes': ['CD14', 'LYZ']
}

for ct, genes in markers.items():
    ct_cells = adata_query[adata_query.obs['predicted_cell_type'] == ct]
    if len(ct_cells) > 0:
        for gene in genes:
            if gene in adata_query.var_names:
                expr = ct_cells[:, gene].X.mean()
                print(f"{ct} - {gene}: {expr:.3f}")
```

## 完整流程

```python
def transfer_labels(
    adata_ref,
    adata_query,
    cell_type_key="cell_type",
    batch_key=None,
    n_top_genes=3000,
    confidence_threshold=0.5
):
    """
    从参考数据向查询数据转移细胞类型标签。

    Parameters
    ----------
    adata_ref : AnnData
        带注释的参考数据
    adata_query : AnnData
        未注释的查询数据
    cell_type_key : str
        参考数据中含细胞类型注释的列
    batch_key : str, optional
        批次列
    n_top_genes : int
        HVG数量
    confidence_threshold : float
        预测的最低置信度

    Returns
    -------
    含预测的AnnData
    """
    import scvi
    import scanpy as sc

    # 准备参考数据
    adata_ref = adata_ref.copy()
    adata_ref.layers["counts"] = adata_ref.X.copy()

    sc.pp.highly_variable_genes(
        adata_ref,
        n_top_genes=n_top_genes,
        flavor="seurat_v3",
        batch_key=batch_key,
        layer="counts"
    )
    adata_ref = adata_ref[:, adata_ref.var["highly_variable"]].copy()

    # 训练参考模型
    scvi.model.SCVI.setup_anndata(adata_ref, layer="counts", batch_key=batch_key)
    scvi_ref = scvi.model.SCVI(adata_ref, n_latent=30)
    scvi_ref.train(max_epochs=200)

    scanvi_ref = scvi.model.SCANVI.from_scvi_model(
        scvi_ref,
        labels_key=cell_type_key,
        unlabeled_category="Unknown"
    )
    scanvi_ref.train(max_epochs=50)

    # 准备查询数据
    adata_query = adata_query[:, adata_ref.var_names].copy()
    adata_query.layers["counts"] = adata_query.X.copy()

    # 映射查询数据
    scvi.model.SCANVI.prepare_query_anndata(adata_query, scanvi_ref)
    scanvi_query = scvi.model.SCANVI.load_query_data(adata_query, scanvi_ref)
    scanvi_query.train(max_epochs=100, plan_kwargs={"weight_decay": 0.0})

    # 获取预测
    adata_query.obs["predicted_cell_type"] = scanvi_query.predict()
    soft = scanvi_query.predict(soft=True)
    adata_query.obs["prediction_score"] = soft.max(axis=1)

    # 标记低置信度
    adata_query.obs["confident_prediction"] = adata_query.obs["prediction_score"] >= confidence_threshold

    # 添加潜在表示
    adata_query.obsm["X_scANVI"] = scanvi_query.get_latent_representation()

    return adata_query, scanvi_ref, scanvi_query

# 用法
adata_annotated, ref_model, query_model = transfer_labels(
    adata_ref,
    adata_query,
    cell_type_key="cell_type"
)

# 可视化
sc.pp.neighbors(adata_annotated, use_rep="X_scANVI")
sc.tl.umap(adata_annotated)
sc.pl.umap(adata_annotated, color=['predicted_cell_type', 'prediction_score'])
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 低置信度预测较多 | 查询数据含新型细胞类型 | 手动注释低置信度细胞 |
| 预测错误 | 参考数据与组织不匹配 | 使用与组织相适应的参考 |
| 基因不匹配 | 基因命名不同 | 转换基因ID |
| 全部预测相同 | 查询数据差异过大 | 检查数据质量，尝试不同参考 |

## 关键参考文献

- Xu et al. (2021) "Probabilistic harmonization and annotation of single-cell transcriptomics data with deep generative models"
- Lotfollahi et al. (2022) "Mapping single-cell data to reference atlases by transfer learning"
