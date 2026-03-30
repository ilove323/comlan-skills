# 使用sysVI进行高级批次校正

本参考文档介绍使用sysVI进行系统级批次校正，专为整合跨主要技术或研究差异的数据而设计。

## 概述

sysVI（系统变分推断）在以下场景中扩展了scVI：
- 批次效应非常强烈（不同技术）
- 标准scVI过度校正了生物学信号
- 需要将"系统"效应与生物学变异分离

## 何时使用sysVI与scVI

| 场景 | 推荐模型 |
|------|---------|
| 相同技术，不同样本 | scVI |
| 10x v2与10x v3 | scVI（通常） |
| 10x与Smart-seq2 | sysVI |
| 不同测序深度 | 带协变量的scVI |
| 跨研究整合 | sysVI |
| 图谱规模整合 | sysVI |

## 先决条件

```python
import scvi
import scanpy as sc
import numpy as np

print(f"scvi-tools version: {scvi.__version__}")
```

## 理解sysVI架构

sysVI将变异分为：
1. **生物学变异**：细胞类型、状态、轨迹
2. **系统变异**：技术、研究、实验室效应

```
                    ┌─────────────────┐
Input counts ──────►│    Encoder      │
                    │                 │
System info ───────►│  (conditioned)  │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │   Latent z      │
                    │  (biological)   │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
System info ───────►│    Decoder      │
                    │  (conditioned)  │
                    └────────┬────────┘
                             │
                    Reconstructed counts
```

## 基本sysVI工作流程

### 第1步：准备数据

```python
# 加载来自不同系统的数据集
adata1 = sc.read_h5ad("10x_data.h5ad")
adata2 = sc.read_h5ad("smartseq_data.h5ad")

# 添加系统标签
adata1.obs["system"] = "10x"
adata2.obs["system"] = "Smart-seq2"

# 添加批次标签（系统内部）
# 例如，每种技术内的不同样本

# 拼接
adata = sc.concat([adata1, adata2])

# 存储原始计数
adata.layers["counts"] = adata.X.copy()
```

### 第2步：HVG选择

```python
# 考虑批次和系统进行HVG选择
sc.pp.highly_variable_genes(
    adata,
    n_top_genes=4000,  # 跨系统使用更多基因
    flavor="seurat_v3",
    batch_key="system",  # 考虑系统进行HVG选择
    layer="counts"
)

# 可选：确保系统间的重叠
# 检查HVG在两个系统中都有表达
adata = adata[:, adata.var["highly_variable"]].copy()
```

### 第3步：设置并训练sysVI

```python
# 设置AnnData
# 注意：sysVI的访问方式可能因版本而异
# 请查阅scvi-tools文档了解当前API

scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="sample",           # 系统内批次
    categorical_covariate_keys=["system"]  # 系统级协变量
)

# 对于真正的sysVI（如果您的版本支持）
# scvi.model.SysVI.setup_anndata(...)

# 创建具有系统感知的模型
model = scvi.model.SCVI(
    adata,
    n_latent=30,
    n_layers=2,
    gene_likelihood="nb"
)

# 训练
model.train(max_epochs=300)
```

### 第4步：提取表示

```python
# 获取潜在表示
adata.obsm["X_integrated"] = model.get_latent_representation()

# 聚类和可视化
sc.pp.neighbors(adata, use_rep="X_integrated")
sc.tl.umap(adata)
sc.tl.leiden(adata)

# 检查整合效果
sc.pl.umap(adata, color=["system", "leiden", "cell_type"])
```

## 替代方案：Harmony + scVI

对于跨系统整合，联合使用方法效果良好：

```python
import scanpy.external as sce

# 首先运行PCA
sc.pp.pca(adata)

# 应用Harmony进行初始对齐
sce.pp.harmony_integrate(adata, key="system")

# 然后在Harmony校正的嵌入上训练scVI
# 或直接使用Harmony表示
```

## 替代方案：在scVI中使用协变量

对于中等程度的系统效应：

```python
# 将系统作为分类协变量
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="sample",
    categorical_covariate_keys=["system", "technology_version"]
)

model = scvi.model.SCVI(adata, n_latent=30)
model.train()
```

## 替代方案：独立模型+整合

对于差异极大的系统：

```python
# 训练独立模型
scvi.model.SCVI.setup_anndata(adata1, layer="counts", batch_key="sample")
model1 = scvi.model.SCVI(adata1)
model1.train()

scvi.model.SCVI.setup_anndata(adata2, layer="counts", batch_key="sample")
model2 = scvi.model.SCVI(adata2)
model2.train()

# 获取潜在空间
adata1.obsm["X_scVI"] = model1.get_latent_representation()
adata2.obsm["X_scVI"] = model2.get_latent_representation()

# 使用CCA或Harmony对齐
# ... 额外的对齐步骤
```

## 评估跨系统整合

### 视觉评估

```python
import matplotlib.pyplot as plt

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# 按系统着色
sc.pl.umap(adata, color="system", ax=axes[0], show=False, title="By System")

# 按细胞类型着色
sc.pl.umap(adata, color="cell_type", ax=axes[1], show=False, title="By Cell Type")

# 按标志物表达着色
sc.pl.umap(adata, color="CD3D", ax=axes[2], show=False, title="CD3D Expression")

plt.tight_layout()
```

### 定量指标

```python
# 使用scib-metrics
from scib_metrics.benchmark import Benchmarker

bm = Benchmarker(
    adata,
    batch_key="system",
    label_key="cell_type",
    embedding_obsm_keys=["X_integrated"]
)

bm.benchmark()

# 关键指标：
# - 批次混合（ASW_batch，Graph connectivity）
# - 生物学保留（NMI，ARI，ASW_label）
```

### LISI评分

```python
# 局部逆辛普森指数
from scib_metrics import lisi

# 批次LISI（越高=混合越好）
batch_lisi = lisi.ilisi_graph(
    adata,
    batch_key="system",
    use_rep="X_integrated"
)

# 细胞类型LISI（越低=保留越好）
ct_lisi = lisi.clisi_graph(
    adata,
    label_key="cell_type",
    use_rep="X_integrated"
)

print(f"Batch LISI: {batch_lisi.mean():.3f}")
print(f"Cell type LISI: {ct_lisi.mean():.3f}")
```

## 处理特定挑战

### 不同基因集

```python
# 找到共同基因
common_genes = adata1.var_names.intersection(adata2.var_names)
print(f"Common genes: {len(common_genes)}")

# 如果太少，使用基因映射
# 或插补缺失基因
```

### 不同测序深度

```python
# 将深度作为连续协变量
adata.obs["log_counts"] = np.log1p(adata.obs["total_counts"])

scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    batch_key="sample",
    continuous_covariate_keys=["log_counts"]
)
```

### 不平衡的细胞类型

```python
# 检查每个系统的细胞类型分布
import pandas as pd

ct_dist = pd.crosstab(adata.obs["system"], adata.obs["cell_type"], normalize="index")
print(ct_dist)

# 如果非常不平衡，考虑：
# 1. 子采样以平衡
# 2. 使用带标签的scANVI保留稀有细胞类型
```

## 完整流程

```python
def integrate_cross_system(
    adatas: dict,
    system_key: str = "system",
    batch_key: str = "batch",
    cell_type_key: str = "cell_type",
    n_top_genes: int = 4000,
    n_latent: int = 30
):
    """
    整合来自不同技术系统的数据集。

    Parameters
    ----------
    adatas : dict
        {系统名称: AnnData}的字典
    system_key : str
        系统注释的键
    batch_key : str
        系统内批次的键
    cell_type_key : str
        细胞类型标签的键（可选）
    n_top_genes : int
        HVG数量
    n_latent : int
        潜在维度

    Returns
    -------
    含模型的整合AnnData
    """
    import scvi
    import scanpy as sc

    # 添加系统标签并拼接
    for system_name, adata in adatas.items():
        adata.obs[system_key] = system_name

    adata = sc.concat(list(adatas.values()))

    # 找到共同基因
    for name, ad in adatas.items():
        if name == list(adatas.keys())[0]:
            common_genes = set(ad.var_names)
        else:
            common_genes = common_genes.intersection(ad.var_names)

    adata = adata[:, list(common_genes)].copy()
    print(f"Common genes: {len(common_genes)}")

    # 存储计数
    adata.layers["counts"] = adata.X.copy()

    # HVG选择
    sc.pp.highly_variable_genes(
        adata,
        n_top_genes=n_top_genes,
        flavor="seurat_v3",
        batch_key=system_key,
        layer="counts"
    )
    adata = adata[:, adata.var["highly_variable"]].copy()

    # 设置系统作为协变量
    scvi.model.SCVI.setup_anndata(
        adata,
        layer="counts",
        batch_key=batch_key if batch_key in adata.obs else None,
        categorical_covariate_keys=[system_key]
    )

    # 训练
    model = scvi.model.SCVI(adata, n_latent=n_latent, n_layers=2)
    model.train(max_epochs=300, early_stopping=True)

    # 获取表示
    adata.obsm["X_integrated"] = model.get_latent_representation()

    # 聚类
    sc.pp.neighbors(adata, use_rep="X_integrated")
    sc.tl.umap(adata)
    sc.tl.leiden(adata)

    return adata, model

# 用法
adatas = {
    "10x_v3": sc.read_h5ad("10x_v3_data.h5ad"),
    "Smart-seq2": sc.read_h5ad("smartseq_data.h5ad"),
    "Drop-seq": sc.read_h5ad("dropseq_data.h5ad")
}

adata_integrated, model = integrate_cross_system(adatas)

# 可视化
sc.pl.umap(adata_integrated, color=["system", "leiden"])
```

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 系统未混合 | 效应太强 | 使用更多基因，增加n_latent |
| 过度校正 | 模型过于激进 | 减少n_layers，使用scANVI |
| 共同基因太少 | 不同平台 | 使用基因名称映射 |
| 一个系统主导 | 大小不平衡 | 对较大数据集进行子采样 |

## 关键参考文献

- Lopez et al. (2018) "Deep generative modeling for single-cell transcriptomics"
- Luecken et al. (2022) "Benchmarking atlas-level data integration in single-cell genomics"
