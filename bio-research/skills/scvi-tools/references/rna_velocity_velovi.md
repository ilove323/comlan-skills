# 使用veloVI进行RNA速率分析

本参考文档介绍使用veloVI进行RNA速率分析，这是一种改进传统速率方法的深度学习方法。

## 概述

RNA速率通过以下建模来估计细胞的未来状态：
- **未剪接RNA**：新转录的，含内含子
- **剪接RNA**：成熟mRNA，内含子已去除

未剪接与剪接的比率表明一个基因是否在上调或下调。

## 为什么使用veloVI？

传统方法（velocyto、scVelo）存在局限性：
- 假设稳态或动力学模型
- 对噪声敏感
- 不处理批次效应

veloVI通过以下方式解决这些问题：
- 概率建模
- 更好的不确定性量化
- 与scVI框架集成

## 先决条件

```python
import scvi
import scvelo as scv
import scanpy as sc
import numpy as np

print(f"scvi-tools version: {scvi.__version__}")
print(f"scvelo version: {scv.__version__}")
```

## 第1步：生成剪接/未剪接计数

### 来自BAM文件（velocyto）

```bash
# 在Cell Ranger输出上运行velocyto
velocyto run10x /path/to/cellranger_output /path/to/genes.gtf

# 输出：含spliced/unspliced层的velocyto.loom文件
```

### 来自kb-python（kallisto|bustools）

```bash
# 使用kallisto的更快替代方案
kb count \
    --workflow lamanno \
    -i index.idx \
    -g t2g.txt \
    -c1 spliced_t2c.txt \
    -c2 unspliced_t2c.txt \
    -x 10xv3 \
    -o output \
    R1.fastq.gz R2.fastq.gz
```

## 第2步：加载速率数据

```python
# 加载来自velocyto的loom文件
adata = scv.read("velocyto_output.loom")

# 或从kb-python加载
adata = sc.read_h5ad("adata.h5ad")
# 剪接数据在adata.layers["spliced"]中
# 未剪接数据在adata.layers["unspliced"]中

# 检查层
print("Available layers:", list(adata.layers.keys()))
print(f"Spliced shape: {adata.layers['spliced'].shape}")
print(f"Unspliced shape: {adata.layers['unspliced'].shape}")
```

### 与现有AnnData合并

```python
# 如果有独立的loom文件和h5ad文件
ldata = scv.read("velocyto.loom")
adata = sc.read_h5ad("processed.h5ad")

# 将速率数据合并到已处理的adata中
adata = scv.utils.merge(adata, ldata)
```

## 第3步：速率分析预处理

```python
# 过滤和标准化
scv.pp.filter_and_normalize(
    adata,
    min_shared_counts=20,
    n_top_genes=2000
)

# 计算矩（用于scVelo比较）
scv.pp.moments(adata, n_pcs=30, n_neighbors=30)
```

## 第4步：运行veloVI

### 设置AnnData

```python
# 为veloVI设置
scvi.model.VELOVI.setup_anndata(
    adata,
    spliced_layer="spliced",
    unspliced_layer="unspliced"
)
```

### 训练模型

```python
# 创建并训练veloVI模型
vae = scvi.model.VELOVI(adata)

vae.train(
    max_epochs=500,
    early_stopping=True,
    batch_size=256
)

# 检查训练
vae.history["elbo_train"].plot()
```

### 获取速率估计

```python
# 获取潜在时间
latent_time = vae.get_latent_time(n_samples=25)
adata.obs["veloVI_latent_time"] = latent_time

# 获取速率
velocities = vae.get_velocity(n_samples=25)
adata.layers["veloVI_velocity"] = velocities

# 获取表达状态
adata.layers["veloVI_expression"] = vae.get_expression_fit(n_samples=25)
```

## 第5步：可视化速率

### 速率流线

```python
# 计算速率图
scv.tl.velocity_graph(adata, vkey="veloVI_velocity")

# 在UMAP上绘制流线
scv.pl.velocity_embedding_stream(
    adata,
    basis="umap",
    vkey="veloVI_velocity",
    color="cell_type"
)
```

### 速率箭头

```python
# 单细胞箭头
scv.pl.velocity_embedding(
    adata,
    basis="umap",
    vkey="veloVI_velocity",
    arrow_length=3,
    arrow_size=2,
    color="cell_type"
)
```

### 潜在时间

```python
# 绘制潜在时间（来自速率的伪时间）
sc.pl.umap(adata, color="veloVI_latent_time", cmap="viridis")
```

## 第6步：与scVelo比较

```python
# 运行标准scVelo进行比较
scv.tl.velocity(adata, mode="dynamical")
scv.tl.velocity_graph(adata)

# 比较速率场
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

scv.pl.velocity_embedding_stream(
    adata, basis="umap", ax=axes[0],
    title="scVelo", show=False
)

scv.pl.velocity_embedding_stream(
    adata, basis="umap", vkey="veloVI_velocity",
    ax=axes[1], title="veloVI", show=False
)

plt.tight_layout()
```

## 第7步：基因级分析

### 速率相图

```python
# 绘制特定基因的相图
genes = ["SOX2", "PAX6", "DCX", "NEUROD1"]

scv.pl.velocity(
    adata,
    var_names=genes,
    vkey="veloVI_velocity",
    colorbar=True
)
```

### 基因动力学

```python
# 绘制潜在时间上的表达
for gene in genes:
    fig, ax = plt.subplots(figsize=(6, 4))

    sc.pl.scatter(
        adata,
        x="veloVI_latent_time",
        y=gene,
        color="cell_type",
        ax=ax,
        show=False
    )
    ax.set_xlabel("Latent Time")
    ax.set_ylabel(f"{gene} Expression")
```

### 驱动基因

```python
# 找到驱动速率的基因
scv.tl.rank_velocity_genes(
    adata,
    vkey="veloVI_velocity",
    groupby="cell_type"
)

# 获取每个聚类的顶部基因
df = scv.get_df(adata, "rank_velocity_genes/names")
print(df.head(10))
```

## 第8步：不确定性量化

veloVI提供不确定性估计：

```python
# 获取含不确定性的速率
velocity_mean, velocity_std = vae.get_velocity(
    n_samples=100,
    return_mean=True,
    return_numpy=True
)

# 存储不确定性
adata.layers["velocity_uncertainty"] = velocity_std

# 可视化不确定性
adata.obs["mean_velocity_uncertainty"] = velocity_std.mean(axis=1)
sc.pl.umap(adata, color="mean_velocity_uncertainty")
```

## 完整流程

```python
def run_velocity_analysis(
    adata,
    spliced_layer="spliced",
    unspliced_layer="unspliced",
    n_top_genes=2000,
    max_epochs=500
):
    """
    使用veloVI进行完整的RNA速率分析。

    Parameters
    ----------
    adata : AnnData
        含spliced/unspliced层的数据
    spliced_layer : str
        剪接计数的层名
    unspliced_layer : str
        未剪接计数的层名
    n_top_genes : int
        速率基因数量
    max_epochs : int
        训练epoch数

    Returns
    -------
    含速率的AnnData和模型
    """
    import scvi
    import scvelo as scv
    import scanpy as sc

    adata = adata.copy()

    # 预处理
    scv.pp.filter_and_normalize(
        adata,
        min_shared_counts=20,
        n_top_genes=n_top_genes
    )

    # 计算矩（某些可视化所需）
    scv.pp.moments(adata, n_pcs=30, n_neighbors=30)

    # 设置veloVI
    scvi.model.VELOVI.setup_anndata(
        adata,
        spliced_layer=spliced_layer,
        unspliced_layer=unspliced_layer
    )

    # 训练
    model = scvi.model.VELOVI(adata)
    model.train(max_epochs=max_epochs, early_stopping=True)

    # 获取结果
    adata.obs["latent_time"] = model.get_latent_time(n_samples=25)
    adata.layers["velocity"] = model.get_velocity(n_samples=25)

    # 计算速率图以进行可视化
    scv.tl.velocity_graph(adata, vkey="velocity")

    # 如果没有UMAP则计算
    if "X_umap" not in adata.obsm:
        sc.pp.neighbors(adata)
        sc.tl.umap(adata)

    return adata, model

# 用法
adata_velocity, model = run_velocity_analysis(adata)

# 可视化
scv.pl.velocity_embedding_stream(
    adata_velocity,
    basis="umap",
    vkey="velocity",
    color="cell_type"
)

sc.pl.umap(adata_velocity, color="latent_time")
```

## 进阶：批次感知速率

```python
# 对于多批次数据，在模型中包含批次
scvi.model.VELOVI.setup_anndata(
    adata,
    spliced_layer="spliced",
    unspliced_layer="unspliced",
    batch_key="batch"
)

model = scvi.model.VELOVI(adata)
model.train()
```

## 解读结果

### 良好的速率信号

- 流线遵循预期的分化方向
- 潜在时间与已知生物学相关
- 相图显示清晰的动力学

### 较差的速率信号

- 随机/混乱的流线
- 与已知标志物无相关性
- 可能表明：
  - 未剪接reads不足
  - 细胞处于稳态
  - 技术问题

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 无速率信号 | 未剪接计数少 | 检查测序深度，使用kb-python |
| 方向相反 | 根细胞分配错误 | 手动设置根细胞 |
| 流线嘈杂 | 基因太多 | 减少n_top_genes |
| 内存错误 | 数据集太大 | 减小batch_size |

## 关键参考文献

- Gayoso et al. (2023) "Deep generative modeling of transcriptional dynamics for RNA velocity analysis in single cells"
- La Manno et al. (2018) "RNA velocity of single cells"
- Bergen et al. (2020) "Generalizing RNA velocity to transient cell states through dynamical modeling"
