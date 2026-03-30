# scvi-tools环境配置

本参考文档介绍scvi-tools的安装和环境配置。

## 安装选项

### 选项1：Conda环境（推荐）

```bash
# 创建支持GPU的环境
conda create -n scvi-env python=3.10
conda activate scvi-env

# 安装scvi-tools
pip install scvi-tools

# GPU加速（推荐用于大型数据集）
pip install torch --index-url https://download.pytorch.org/whl/cu118

# 常用依赖
pip install scanpy leidenalg
```

### 选项2：仅使用Pip

```bash
# 创建虚拟环境
python -m venv scvi-env
source scvi-env/bin/activate  # Linux/Mac
# scvi-env\Scripts\activate   # Windows

# 安装
pip install scvi-tools scanpy
```

### 选项3：含空间分析支持

```bash
conda create -n scvi-spatial python=3.10
conda activate scvi-spatial

pip install scvi-tools scanpy squidpy
```

### 选项4：含MuData支持（多组学）

```bash
pip install scvi-tools mudata muon
```

## 验证安装

```python
import scvi
import torch
import scanpy as sc

print(f"scvi-tools version: {scvi.__version__}")
print(f"scanpy version: {sc.__version__}")
print(f"PyTorch version: {torch.__version__}")
print(f"GPU available: {torch.cuda.is_available()}")

if torch.cuda.is_available():
    print(f"GPU device: {torch.cuda.get_device_name(0)}")
    print(f"GPU memory: {torch.cuda.get_device_properties(0).total_memory / 1e9:.1f} GB")
```

## GPU配置

### 检查CUDA版本

```bash
nvidia-smi
nvcc --version
```

### PyTorch CUDA版本

| CUDA版本 | PyTorch安装命令 |
|----------|----------------|
| CUDA 11.8 | `pip install torch --index-url https://download.pytorch.org/whl/cu118` |
| CUDA 12.1 | `pip install torch --index-url https://download.pytorch.org/whl/cu121` |
| 仅CPU | `pip install torch --index-url https://download.pytorch.org/whl/cpu` |

### 内存管理

```python
import torch

# 在模型之间清除GPU缓存
torch.cuda.empty_cache()

# 监控内存使用
print(f"Allocated: {torch.cuda.memory_allocated() / 1e9:.2f} GB")
print(f"Cached: {torch.cuda.memory_reserved() / 1e9:.2f} GB")
```

## 常见问题

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| `CUDA out of memory` | GPU内存耗尽 | 减小batch_size，使用较小模型 |
| `No GPU detected` | CUDA未安装 | 安装与PyTorch匹配的CUDA工具包 |
| `Version mismatch` | PyTorch/CUDA不兼容 | 使用正确的CUDA版本重新安装PyTorch |
| `Import error scvi` | 缺少依赖 | `pip install scvi-tools[all]` |

## Jupyter配置

```bash
# 安装Jupyter内核
pip install ipykernel
python -m ipykernel install --user --name scvi-env --display-name "scvi-tools"

# 用于交互式绘图
pip install matplotlib seaborn
```

## 推荐包版本

为了可重复性，锁定版本：

```bash
pip install \
    scvi-tools>=1.0.0 \
    scanpy>=1.9.0 \
    anndata>=0.9.0 \
    torch>=2.0.0
```

## 版本兼容性指南

### scvi-tools 1.x vs 0.x API变更

1.x版本引入了不兼容的变更。主要差异：

| 操作 | 0.x API（已弃用） | 1.x API（当前） |
|------|-----------------|----------------|
| 设置数据 | `scvi.data.setup_anndata(adata, ...)` | `scvi.model.SCVI.setup_anndata(adata, ...)` |
| 注册数据 | `scvi.data.register_tensor_from_anndata(...)` | 内置于`setup_anndata` |
| 查看设置 | `scvi.data.view_anndata_setup(adata)` | `scvi.model.SCVI.view_anndata_setup(adata)` |

### 从0.x迁移到1.x

```python
# 旧API（0.x）——已弃用
import scvi
scvi.data.setup_anndata(adata, layer="counts", batch_key="batch")
model = scvi.model.SCVI(adata)

# 新API（1.x）——当前
import scvi
scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key="batch")
model = scvi.model.SCVI(adata)
```

### 模型特定设置（1.x）

每个模型有自己的setup方法：

```python
# scVI
scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key="batch")

# scANVI
scvi.model.SCANVI.setup_anndata(adata, layer="counts", batch_key="batch", labels_key="cell_type")

# totalVI
scvi.model.TOTALVI.setup_anndata(adata, layer="counts", protein_expression_obsm_key="protein")

# MultiVI（使用MuData）
scvi.model.MULTIVI.setup_mudata(mdata, rna_layer="counts", atac_layer="counts")

# PeakVI
scvi.model.PEAKVI.setup_anndata(adata, batch_key="batch")

# veloVI
scvi.external.VELOVI.setup_anndata(adata, spliced_layer="spliced", unspliced_layer="unspliced")
```

### 最低版本要求

| 包 | 最低版本 | 备注 |
|----|---------|------|
| scvi-tools | 1.0.0 | 当前API所需 |
| scanpy | 1.9.0 | HVG选择改进 |
| anndata | 0.9.0 | 改进的MuData支持 |
| torch | 2.0.0 | 性能改进 |
| mudata | 0.2.0 | MultiVI所需 |
| scvelo | 0.2.5 | veloVI所需 |

### 检查你的版本

```python
import scvi
import scanpy as sc
import anndata
import torch

print(f"scvi-tools: {scvi.__version__}")
print(f"scanpy: {sc.__version__}")
print(f"anndata: {anndata.__version__}")
print(f"torch: {torch.__version__}")

# 检查是否使用1.x API
if hasattr(scvi.model.SCVI, 'setup_anndata'):
    print("Using scvi-tools 1.x API")
else:
    print("WARNING: Using deprecated 0.x API - please upgrade")
```

### 已知兼容性问题

| 问题 | 受影响版本 | 解决方案 |
|------|----------|---------|
| `setup_anndata`未找到 | scvi-tools < 1.0 | 升级到1.0+ |
| MuData错误 | mudata < 0.2 | `pip install mudata>=0.2.0` |
| CUDA版本不匹配 | 任何版本 | 为你的CUDA重新安装PyTorch |
| numpy 2.0问题 | 2024年初版本 | `pip install numpy<2.0` |

### 升级scvi-tools

```bash
# 升级到最新版本
pip install --upgrade scvi-tools

# 升级所有依赖
pip install --upgrade scvi-tools scanpy anndata torch

# 如有问题，全新安装
pip uninstall scvi-tools
pip cache purge
pip install scvi-tools
```

## 测试安装

```python
# 使用示例数据快速测试
import scvi
import scanpy as sc

# 加载测试数据集
adata = scvi.data.heart_cell_atlas_subsampled()
print(f"Loaded test data: {adata.shape}")

# 设置并创建模型（快速测试）
scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key="cell_source")
model = scvi.model.SCVI(adata, n_latent=10)
print("Model created successfully")

# 快速训练测试（1个epoch）
model.train(max_epochs=1)
print("Training works!")
```
