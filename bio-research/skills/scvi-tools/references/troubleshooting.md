# scvi-tools故障排除指南

本参考文档提供诊断和解决所有scvi-tools模型常见问题的综合指南。

## 快速诊断

| 症状 | 可能原因 | 快速修复 |
|------|---------|---------|
| "X should contain integers" | X中含有标准化数据 | 在setup中使用`layer="counts"` |
| CUDA out of memory | GPU内存耗尽 | 减小`batch_size`，使用更小的模型 |
| 训练损失为NaN | 数据不良或学习率问题 | 检查全零细胞/基因 |
| 批次未混合 | 共享特征太少 | 增加HVG，检查基因重叠 |
| 过度校正 | 整合过于激进 | 使用含标签的scANVI |
| 导入错误 | 缺少依赖 | `pip install scvi-tools[all]` |

## 数据格式问题

### 问题：来自Seurat的CITE-seq蛋白质数据是CLR标准化的

**原因**：Seurat的`NormalizeData(normalization.method = "CLR")`会转换原始ADT计数。totalVI需要蛋白质数据的原始整数计数。

**症状**：
- 蛋白质值不是整数
- 蛋白质值包含负数
- 模型训练结果不佳

**解决方案**：
```python
# 检查蛋白质数据是否已标准化
protein = adata.obsm["protein_expression"]
print(f"Min value: {protein.min()}")  # 如果是原始计数应为0
print(f"Contains integers: {np.allclose(protein, protein.astype(int))}")

# 如果从Seurat导入，使用原始计数assay，而非标准化后的
# 在R/Seurat中，导出RNA assay的counts slot，而非data slot
# GetAssayData(seurat_obj, assay = "ADT", slot = "counts")
```

### 问题："layer not found"或"X should contain integers"

**原因**：scvi-tools需要原始整数计数，而非标准化数据。

**解决方案**：
```python
# 检查X是否包含整数
import numpy as np
print(f"X max: {adata.X.max()}")
print(f"Contains integers: {np.allclose(adata.X.data, adata.X.data.astype(int))}")

# 如果已标准化，从原始数据恢复
if hasattr(adata, 'raw') and adata.raw is not None:
    adata = adata.raw.to_adata()

# 或使用现有的counts层
adata.layers["counts"] = adata.X.copy()
scvi.model.SCVI.setup_anndata(adata, layer="counts")
```

### 问题：稀疏矩阵错误

**原因**：不兼容的稀疏格式或期望密集数组。

**解决方案**：
```python
from scipy.sparse import csr_matrix

# 转换为CSR格式（兼容性最好）
if hasattr(adata.X, 'toarray'):
    adata.X = csr_matrix(adata.X)

# 如果数据量足够小，转换为密集格式
if adata.n_obs * adata.n_vars < 1e8:
    adata.X = adata.X.toarray()
```

### 问题：数据中含有NaN或Inf值

**原因**：缺失值或数据损坏。

**解决方案**：
```python
import numpy as np

# 检查问题
X = adata.X.toarray() if hasattr(adata.X, 'toarray') else adata.X
print(f"NaN count: {np.isnan(X).sum()}")
print(f"Inf count: {np.isinf(X).sum()}")
print(f"Negative count: {(X < 0).sum()}")

# 将NaN/Inf替换为0
X = np.nan_to_num(X, nan=0, posinf=0, neginf=0)
X = np.clip(X, 0, None)  # 确保非负值
adata.X = csr_matrix(X)
```

### 问题：batch_key或labels_key未找到

**原因**：adata.obs中的列名不匹配。

**解决方案**：
```python
# 列出可用列
print(adata.obs.columns.tolist())

# 检查相似名称
for col in adata.obs.columns:
    if 'batch' in col.lower() or 'sample' in col.lower():
        print(f"Potential batch column: {col}")
```

## GPU和内存问题

### 问题：CUDA内存不足

**原因**：模型或批次无法放入GPU内存。

**解决方案**（按顺序尝试）：

```python
# 1. 减小批次大小
model.train(batch_size=64)  # 默认为128

# 2. 使用更小的模型架构
model = scvi.model.SCVI(
    adata,
    n_latent=10,   # 默认为10-30
    n_layers=1     # 默认为1-2
)

# 3. 子集至更少的基因
sc.pp.highly_variable_genes(adata, n_top_genes=1500)
adata = adata[:, adata.var['highly_variable']].copy()

# 4. 在模型之间清除GPU缓存
import torch
torch.cuda.empty_cache()

# 5. 如果GPU太小，使用CPU
model.train(accelerator="cpu")
```

### 问题：未检测到GPU

**原因**：CUDA未安装或版本不匹配。

**诊断**：
```python
import torch
print(f"CUDA available: {torch.cuda.is_available()}")
print(f"PyTorch version: {torch.__version__}")
print(f"CUDA version: {torch.version.cuda}")
```

**解决方案**：
```bash
# 检查系统CUDA
nvidia-smi
nvcc --version

# 重新安装与CUDA匹配的PyTorch
pip install torch --index-url https://download.pytorch.org/whl/cu118  # CUDA 11.8
# 或
pip install torch --index-url https://download.pytorch.org/whl/cu121  # CUDA 12.1
```

### 问题：大型数据集内存错误

**原因**：数据集太大，无法放入系统RAM。

**解决方案**：
```python
# 1. 分块处理（适用于超大数据）
# 先子采样进行初步探索
adata_sample = adata[np.random.choice(adata.n_obs, 50000, replace=False)].copy()

# 2. 使用AnnData的backed模式
adata = sc.read_h5ad("large_data.h5ad", backed='r')

# 3. 更激进地减少基因数量
adata = adata[:, adata.var['highly_variable']].copy()
```

## 训练问题

### 问题：训练损失为NaN

**原因**：数值不稳定、数据不良或学习率问题。

**解决方案**：
```python
# 1. 检查有问题的细胞/基因
sc.pp.filter_cells(adata, min_genes=200)
sc.pp.filter_genes(adata, min_cells=3)

# 2. 删除计数为零的细胞
adata = adata[adata.X.sum(axis=1) > 0].copy()

# 3. 使用梯度裁剪（scvi-tools内置）
model.train(max_epochs=200, early_stopping=True)
```

### 问题：训练不收敛

**原因**：epoch数不足、超参数不佳或数据问题。

**解决方案**：
```python
# 1. 延长训练
model.train(max_epochs=400)

# 2. 检查训练曲线
import matplotlib.pyplot as plt
plt.plot(model.history['elbo_train'])
plt.plot(model.history['elbo_validation'])
plt.xlabel('Epoch')
plt.ylabel('ELBO')
plt.legend(['Train', 'Validation'])

# 3. 根据数据规模调整模型大小
# 小型数据（<10k细胞）：使用更小的模型
model = scvi.model.SCVI(adata, n_latent=10, n_layers=1, dropout_rate=0.2)

# 大型数据（>100k细胞）：可以使用更大的模型
model = scvi.model.SCVI(adata, n_latent=30, n_layers=2)
```

### 问题：过拟合（验证损失上升）

**原因**：模型过于复杂或训练时间过长。

**解决方案**：
```python
# 1. 启用早停
model.train(early_stopping=True, early_stopping_patience=10)

# 2. 添加正则化
model = scvi.model.SCVI(adata, dropout_rate=0.2)

# 3. 降低模型复杂度
model = scvi.model.SCVI(adata, n_layers=1)
```

## 整合问题

### 问题：批次未混合

**原因**：共享特征太少、生物学差异过大或技术问题。

**解决方案**：
```python
# 1. 检查批次间的基因重叠
for batch in adata.obs['batch'].unique():
    batch_genes = adata[adata.obs['batch'] == batch].var_names
    print(f"{batch}: {len(batch_genes)} genes")

# 2. 使用更多HVG
sc.pp.highly_variable_genes(adata, n_top_genes=4000, batch_key="batch")

# 3. 延长训练
model.train(max_epochs=400)

# 4. 增加潜在维度
model = scvi.model.SCVI(adata, n_latent=50)
```

### 问题：过度校正（生物学信号丢失）

**原因**：模型去除了过多的变异。

**解决方案**：
```python
# 1. 使用含细胞类型标签的scANVI
scvi.model.SCANVI.from_scvi_model(scvi_model, labels_key="cell_type")

# 2. 降低模型容量
model = scvi.model.SCVI(adata, n_latent=10)

# 3. 使用分类协变量代替batch_key
scvi.model.SCVI.setup_anndata(
    adata,
    layer="counts",
    categorical_covariate_keys=["batch"]  # 比batch_key侵入性更小
)
```

### 问题：一个批次主导聚类

**原因**：批次大小不平衡或整合不完全。

**解决方案**：
```python
# 1. 检查批次分布
print(adata.obs['batch'].value_counts())

# 2. 子采样以平衡
from sklearn.utils import resample
balanced = []
min_size = adata.obs['batch'].value_counts().min()
for batch in adata.obs['batch'].unique():
    batch_data = adata[adata.obs['batch'] == batch]
    balanced.append(batch_data[np.random.choice(len(batch_data), min_size, replace=False)])
adata_balanced = sc.concat(balanced)
```

## 特定模型问题

### scANVI：标签转移效果差

**解决方案**：
```python
# 1. 检查标签分布
print(adata.obs['cell_type'].value_counts())

# 2. 对低置信度细胞使用Unknown
adata.obs.loc[adata.obs['prediction_score'] < 0.5, 'cell_type'] = 'Unknown'

# 3. 先训练scVI更长时间再训练scANVI
scvi_model.train(max_epochs=300)
scanvi_model = scvi.model.SCANVI.from_scvi_model(scvi_model, labels_key="cell_type")
scanvi_model.train(max_epochs=100)
```

### totalVI：蛋白质信号嘈杂

**解决方案**：
```python
# 1. 使用去噪的蛋白质值
_, protein_denoised = model.get_normalized_expression(return_mean=True)

# 2. 检查同型对照
# 同型对照应该表达量低
for i, name in enumerate(adata.uns["protein_names"]):
    if 'isotype' in name.lower():
        print(f"{name}: mean={adata.obsm['protein_expression'][:, i].mean():.1f}")
```

### PeakVI：聚类效果差

**解决方案**：
```python
# 1. 使用更多可变peak
from sklearn.feature_selection import VarianceThreshold
selector = VarianceThreshold(threshold=0.05)
adata = adata[:, selector.fit(adata.X).get_support()].copy()

# 2. 二值化数据
adata.X = (adata.X > 0).astype(np.float32)
```

### MultiVI：模态间细胞数不同

**解决方案**：
```python
# 确保相同细胞按相同顺序排列
common_cells = adata_rna.obs_names.intersection(adata_atac.obs_names)
adata_rna = adata_rna[common_cells].copy()
adata_atac = adata_atac[common_cells].copy()
```

### DestVI：去卷积效果差

**解决方案**：
```python
# 1. 检查基因重叠
common_genes = adata_ref.var_names.intersection(adata_spatial.var_names)
print(f"Common genes: {len(common_genes)}")  # 应大于1000

# 2. 使用与组织匹配的参考
# 参考应包含空间数据中预期的所有细胞类型

# 3. 检查参考质量
print(adata_ref.obs['cell_type'].value_counts())
```

## 版本兼容性

### scvi-tools 1.x与0.x API变更

关键差异：
```python
# 0.x API
scvi.data.setup_anndata(adata, ...)

# 1.x API（当前）
scvi.model.SCVI.setup_anndata(adata, ...)
```

### 检查版本
```python
import scvi
import scanpy as sc
import anndata
import torch

print(f"scvi-tools: {scvi.__version__}")
print(f"scanpy: {sc.__version__}")
print(f"anndata: {anndata.__version__}")
print(f"torch: {torch.__version__}")
```

### 推荐版本（截至2024年底）
```
scvi-tools>=1.0.0
scanpy>=1.9.0
anndata>=0.9.0
torch>=2.0.0
```

## 获取帮助

1. **查阅文档**：https://docs.scvi-tools.org/
2. **GitHub问题**：https://github.com/scverse/scvi-tools/issues
3. **Discourse论坛**：https://discourse.scverse.org/
4. **教程**：https://docs.scvi-tools.org/en/stable/tutorials/index.html

提交问题时，请包含：
- scvi-tools版本（`scvi.__version__`）
- Python版本
- 完整错误回溯
- 最小可复现示例
