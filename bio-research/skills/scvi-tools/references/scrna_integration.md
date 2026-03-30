# 使用scVI和scANVI进行scRNA-seq整合

本参考文档介绍使用scVI（无监督）和scANVI（含细胞类型标签的半监督）进行批次校正和数据集整合。

## 概述

单细胞数据集通常存在来自以下原因的批次效应：
- 不同供者/患者
- 不同实验批次
- 不同技术（10x v2 vs v3）
- 不同研究

scVI和scANVI学习一个共享的潜在空间，在消除批次效应的同时保留生物学变异。

## 选择哪个模型

| 模型 | 使用场景 | 是否需要标签 |
|------|---------|------------|
| **scVI** | 无标签可用，探索性分析 | 否 |
| **scANVI** | 有部分/完整标签，需要更好的保留 | 是（部分可以） |

## scVI整合工作流程

### 第1步：准备数据

```python
import scvi
import scanpy as sc

# 加载数据集
adata1 = sc.read_h5ad("dataset1.h5ad")
adata2 = sc.read_h5ad("dataset2.h5ad")

# 添加批次注释
adata1.obs["batch"] = "batch1"
adata2.obs["batch"] = "batch2"

# 合并
adata = sc.concat([adata1, adata2], label="batch")

# 确保有原始计数
# 如果数据已标准化，从.raw恢复
if hasattr(adata, 'raw') and adata.raw is not None:
    adata = adata.raw.to_adata()

# 存储计数
adata.layers["counts"] = adata.X.copy()
```

### 第2步：跨批次HVG选择

```python
# 考虑批次选择HVG
sc.pp.highly_variable_genes(
    adata,
    n_top_genes=2000,
    flavor="seurat_v3",
    batch_key="batch",
    layer="counts"
)

# 子集至HVG
adata = adata[:, adata.var["highly_variable"]].copy()
```

### 第3步：设置并训练scVI

```python
# 用scVI注册数据
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch"
)

# 创建模型
model = scvi.model.SCVI(
    adata,
    n_latent=30,          # 潜在维度
    n_layers=2,           # 编码器/解码器深度
    gene_likelihood="nb"  # 负二项（或"zinb"）
)

# 训练
model.train(
    max_epochs=200,
    early_stopping=True,
    early_stopping_patience=10,
    batch_size=128
)

# 绘制训练历史
model.history["elbo_train"].plot()
```

### 第4步：获取整合表示

```python
# 获取潜在表示
adata.obsm["X_scVI"] = model.get_latent_representation()

# 用于聚类和可视化
sc.pp.neighbors(adata, use_rep="X_scVI", n_neighbors=15)
sc.tl.umap(adata)
sc.tl.leiden(adata, resolution=1.0)

# 可视化整合效果
sc.pl.umap(adata, color=["batch", "leiden"], ncols=2)
```

### 第5步：保存模型

```python
# 保存模型以供以后使用
model.save("scvi_model/")

# 加载模型
model = scvi.model.SCVI.load("scvi_model/", adata=adata)
```

## scANVI整合工作流程

scANVI通过细胞类型标签扩展scVI，以获得更好的生物学保留。

### 第1步：准备含标签的数据

```python
# 标签应在adata.obs中
# 对未标注的细胞使用"Unknown"
print(adata.obs["cell_type"].value_counts())

# 对于部分标注的数据
# 标记未标注的细胞
adata.obs["cell_type_scanvi"] = adata.obs["cell_type"].copy()
# adata.obs.loc[unlabeled_mask, "cell_type_scanvi"] = "Unknown"
```

### 第2步：选项A——从头训练scANVI

```python
# 为scANVI设置
scvi.model.SCANVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch",
    labels_key="cell_type"
)

# 创建模型
scanvi_model = scvi.model.SCANVI(
    adata,
    n_latent=30,
    n_layers=2
)

# 训练
scanvi_model.train(max_epochs=200)
```

### 第2步：选项B——从scVI初始化scANVI（推荐）

```python
# 首先训练scVI
scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key="batch")
scvi_model = scvi.model.SCVI(adata, n_latent=30)
scvi_model.train(max_epochs=200)

# 从scVI初始化scANVI
scanvi_model = scvi.model.SCANVI.from_scvi_model(
    scvi_model,
    labels_key="cell_type",
    unlabeled_category="Unknown"  # 对于部分标注的数据
)

# 微调scANVI（需要较少的epoch）
scanvi_model.train(max_epochs=50)
```

### 第3步：获取结果

```python
# 潜在表示
adata.obsm["X_scANVI"] = scanvi_model.get_latent_representation()

# 未标注细胞的预测标签
predictions = scanvi_model.predict()
adata.obs["predicted_cell_type"] = predictions

# 预测概率
soft_predictions = scanvi_model.predict(soft=True)

# 可视化
sc.pp.neighbors(adata, use_rep="X_scANVI")
sc.tl.umap(adata)
sc.pl.umap(adata, color=["batch", "cell_type", "predicted_cell_type"])
```

## 比较整合质量

### 视觉评估

```python
import matplotlib.pyplot as plt

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# 整合前（在PCA上）
sc.pp.pca(adata)
sc.pl.pca(adata, color="batch", ax=axes[0], title="Before (PCA)", show=False)

# scVI后
sc.pp.neighbors(adata, use_rep="X_scVI")
sc.tl.umap(adata)
sc.pl.umap(adata, color="batch", ax=axes[1], title="After scVI", show=False)

# scANVI后
sc.pp.neighbors(adata, use_rep="X_scANVI")
sc.tl.umap(adata)
sc.pl.umap(adata, color="batch", ax=axes[2], title="After scANVI", show=False)

plt.tight_layout()
```

### 定量指标（scib）

```python
# pip install scib-metrics

from scib_metrics.benchmark import Benchmarker

bm = Benchmarker(
    adata,
    batch_key="batch",
    label_key="cell_type",
    embedding_obsm_keys=["X_pca", "X_scVI", "X_scANVI"]
)

bm.benchmark()
bm.plot_results_table()
```

## 差异表达

scVI提供考虑批次效应的差异表达：

```python
# 组间差异表达
de_results = model.differential_expression(
    groupby="cell_type",
    group1="T cells",
    group2="B cells"
)

# 过滤显著结果
de_sig = de_results[
    (de_results["is_de_fdr_0.05"] == True) &
    (abs(de_results["lfc_mean"]) > 1)
]

print(de_sig.head(20))
```

## 高级：多个分类协变量

```python
# 包含批次之外的额外协变量
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="batch",
    categorical_covariate_keys=["donor", "technology"]
)

model = scvi.model.SCVI(adata, n_latent=30)
model.train()
```

## 训练技巧

### 对于大型数据集（>100k细胞）

```python
model.train(
    max_epochs=100,      # 需要较少的epoch
    batch_size=256,      # 更大的批次
    train_size=0.9,      # 较少验证
    early_stopping=True
)
```

### 对于小型数据集（<10k细胞）

```python
model = scvi.model.SCVI(
    adata,
    n_latent=10,         # 较小的潜在空间
    n_layers=1,          # 更简单的模型
    dropout_rate=0.2     # 更多正则化
)

model.train(
    max_epochs=400,
    batch_size=64
)
```

### 监控训练

```python
# 检查训练曲线
import matplotlib.pyplot as plt

fig, ax = plt.subplots()
ax.plot(model.history["elbo_train"], label="Train")
ax.plot(model.history["elbo_validation"], label="Validation")
ax.set_xlabel("Epoch")
ax.set_ylabel("ELBO")
ax.legend()

# 应该看到收敛而没有过拟合
```

## 完整流程

```python
def integrate_datasets(
    adatas,
    batch_key="batch",
    labels_key=None,
    n_top_genes=2000,
    n_latent=30
):
    """
    整合多个scRNA-seq数据集。

    Parameters
    ----------
    adatas : dict
        {批次名称: AnnData}的字典
    batch_key : str
        批次注释的键
    labels_key : str, optional
        细胞类型标签的键（如果提供，使用scANVI）
    n_top_genes : int
        HVG数量
    n_latent : int
        潜在维度

    Returns
    -------
    含整合表示的AnnData
    """
    import scvi
    import scanpy as sc

    # 添加批次标签并合并
    for batch_name, adata in adatas.items():
        adata.obs[batch_key] = batch_name

    adata = sc.concat(list(adatas.values()), label=batch_key)

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

    # 训练模型
    if labels_key and labels_key in adata.obs.columns:
        # 使用scANVI
        scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key=batch_key)
        scvi_model = scvi.model.SCVI(adata, n_latent=n_latent)
        scvi_model.train(max_epochs=200)

        model = scvi.model.SCANVI.from_scvi_model(
            scvi_model,
            labels_key=labels_key,
            unlabeled_category="Unknown"
        )
        model.train(max_epochs=50)
        rep_key = "X_scANVI"
    else:
        # 使用scVI
        scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key=batch_key)
        model = scvi.model.SCVI(adata, n_latent=n_latent)
        model.train(max_epochs=200)
        rep_key = "X_scVI"

    # 添加表示
    adata.obsm[rep_key] = model.get_latent_representation()

    # 计算近邻和UMAP
    sc.pp.neighbors(adata, use_rep=rep_key)
    sc.tl.umap(adata)
    sc.tl.leiden(adata)

    return adata, model

# 用法
adatas = {
    "study1": sc.read_h5ad("study1.h5ad"),
    "study2": sc.read_h5ad("study2.h5ad"),
    "study3": sc.read_h5ad("study3.h5ad")
}

adata_integrated, model = integrate_datasets(
    adatas,
    labels_key="cell_type"
)

sc.pl.umap(adata_integrated, color=["batch", "leiden", "cell_type"])
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 批次未混合 | 共享基因太少 | 使用更多HVG，检查基因重叠 |
| 过度校正 | 生物学变异被删除 | 使用含标签的scANVI |
| 训练发散 | 学习率太高 | 降低lr，增大batch_size |
| 损失为NaN | 数据不良 | 检查全零细胞/基因 |
| 内存错误 | 细胞太多 | 减小batch_size，使用GPU |
