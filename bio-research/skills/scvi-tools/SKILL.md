---
name: scvi-tools
description: 使用scvi-tools进行单细胞深度学习分析。当用户需要以下功能时使用本技能：(1) 使用scVI/scANVI进行数据整合和批次校正，(2) 使用PeakVI进行ATAC-seq分析，(3) 使用totalVI进行CITE-seq多模态分析，(4) 使用MultiVI进行多组学RNA+ATAC分析，(5) 使用DestVI进行空间转录组学反卷积，(6) 使用scANVI/scArches进行标签转移和参考映射，(7) 使用veloVI进行RNA速率分析，或 (8) 任何基于深度学习的单细胞方法。触发词包括：scVI、scANVI、totalVI、PeakVI、MultiVI、DestVI、veloVI、sysVI、scArches、变分自动编码器、VAE、批次校正、数据整合、多模态、CITE-seq、多组学、参考映射、潜在空间。触发词：scVI工具、单细胞分析、scvi-tools
---

# scvi-tools深度学习技能

本技能为使用scvi-tools（单细胞基因组学概率模型领先框架）进行基于深度学习的单细胞分析提供指导。

## 如何使用本技能

1. 从下方的模型/工作流表格中确定适合的工作流
2. 阅读对应的参考文件以获取详细步骤和代码
3. 使用 `scripts/` 中的脚本避免重复编写常见代码
4. 如遇安装或GPU问题，请参阅 `references/environment_setup.md`
5. 如需调试，请参阅 `references/troubleshooting.md`

## 何时使用本技能

- 提到scvi-tools、scVI、scANVI或相关模型时
- 需要基于深度学习的批次校正或整合时
- 处理多模态数据（CITE-seq、多组学）时
- 需要参考映射或标签转移时
- 分析ATAC-seq或空间转录组学数据时
- 学习单细胞数据的潜在表示时

## 模型选择指南

| 数据类型 | 模型 | 主要应用场景 |
|----------|------|-------------|
| scRNA-seq | **scVI** | 无监督整合、差异表达、填充 |
| scRNA-seq + 标签 | **scANVI** | 标签转移、半监督整合 |
| CITE-seq（RNA+蛋白质） | **totalVI** | 多模态整合、蛋白质去噪 |
| scATAC-seq | **PeakVI** | 染色质可及性分析 |
| 多组学（RNA+ATAC） | **MultiVI** | 联合模态分析 |
| 空间 + scRNA参考 | **DestVI** | 细胞类型反卷积 |
| RNA速率 | **veloVI** | 转录动力学 |
| 跨技术 | **sysVI** | 系统级批次校正 |

## 工作流参考文件

| 工作流 | 参考文件 | 描述 |
|--------|----------|------|
| 环境配置 | `references/environment_setup.md` | 安装、GPU、版本信息 |
| 数据准备 | `references/data_preparation.md` | 为任意模型格式化数据 |
| scRNA整合 | `references/scrna_integration.md` | scVI/scANVI批次校正 |
| ATAC-seq分析 | `references/atac_peakvi.md` | PeakVI可及性分析 |
| CITE-seq分析 | `references/citeseq_totalvi.md` | totalVI蛋白质+RNA分析 |
| 多组学分析 | `references/multiome_multivi.md` | MultiVI RNA+ATAC分析 |
| 空间反卷积 | `references/spatial_deconvolution.md` | DestVI空间分析 |
| 标签转移 | `references/label_transfer.md` | scANVI参考映射 |
| scArches映射 | `references/scarches_mapping.md` | 查询到参考映射 |
| 批次校正 | `references/batch_correction_sysvi.md` | 高级批次方法 |
| RNA速率 | `references/rna_velocity_velovi.md` | veloVI动力学 |
| 故障排查 | `references/troubleshooting.md` | 常见问题与解决方案 |

## 命令行脚本

用于常见工作流的模块化脚本，可链式调用或按需修改。

### 流程脚本

| 脚本 | 功能 | 用法 |
|------|------|------|
| `prepare_data.py` | QC、过滤、HVG选择 | `python scripts/prepare_data.py raw.h5ad prepared.h5ad --batch-key batch` |
| `train_model.py` | 训练任意scvi-tools模型 | `python scripts/train_model.py prepared.h5ad results/ --model scvi` |
| `cluster_embed.py` | 近邻、UMAP、Leiden | `python scripts/cluster_embed.py adata.h5ad results/` |
| `differential_expression.py` | 差异表达分析 | `python scripts/differential_expression.py model/ adata.h5ad de.csv --groupby leiden` |
| `transfer_labels.py` | 使用scANVI进行标签转移 | `python scripts/transfer_labels.py ref_model/ query.h5ad results/` |
| `integrate_datasets.py` | 多数据集整合 | `python scripts/integrate_datasets.py results/ data1.h5ad data2.h5ad` |
| `validate_adata.py` | 检查数据兼容性 | `python scripts/validate_adata.py data.h5ad --batch-key batch` |

### 示例工作流

```bash
# 1. 验证输入数据
python scripts/validate_adata.py raw.h5ad --batch-key batch --suggest

# 2. 准备数据（QC、HVG选择）
python scripts/prepare_data.py raw.h5ad prepared.h5ad --batch-key batch --n-hvgs 2000

# 3. 训练模型
python scripts/train_model.py prepared.h5ad results/ --model scvi --batch-key batch

# 4. 聚类和可视化
python scripts/cluster_embed.py results/adata_trained.h5ad results/ --resolution 0.8

# 5. 差异表达
python scripts/differential_expression.py results/model results/adata_clustered.h5ad results/de.csv --groupby leiden
```

### Python工具函数

`scripts/model_utils.py` 提供可导入的函数用于自定义工作流：

| 函数 | 功能 |
|------|------|
| `prepare_adata()` | 数据准备（QC、HVG、层设置） |
| `train_scvi()` | 训练scVI或scANVI |
| `evaluate_integration()` | 计算整合指标 |
| `get_marker_genes()` | 提取差异表达标记基因 |
| `save_results()` | 保存模型、数据、图表 |
| `auto_select_model()` | 建议最佳模型 |
| `quick_clustering()` | 近邻 + UMAP + Leiden |

## 关键要求

1. **需要原始计数**：scvi-tools模型需要整数计数数据
   ```python
   adata.layers["counts"] = adata.X.copy()  # 在标准化之前
   scvi.model.SCVI.setup_anndata(adata, layer="counts")
   ```

2. **HVG选择**：使用2000-4000个高变基因
   ```python
   sc.pp.highly_variable_genes(adata, n_top_genes=2000, batch_key="batch", layer="counts", flavor="seurat_v3")
   adata = adata[:, adata.var['highly_variable']].copy()
   ```

3. **批次信息**：为整合指定batch_key
   ```python
   scvi.model.SCVI.setup_anndata(adata, layer="counts", batch_key="batch")
   ```

## 快速决策树

```
需要整合scRNA-seq数据？
├── 有细胞类型标签？ → scANVI (references/label_transfer.md)
└── 无标签？ → scVI (references/scrna_integration.md)

有多模态数据？
├── CITE-seq（RNA + 蛋白质）？ → totalVI (references/citeseq_totalvi.md)
├── 多组学（RNA + ATAC）？ → MultiVI (references/multiome_multivi.md)
└── 仅scATAC-seq？ → PeakVI (references/atac_peakvi.md)

有空间数据？
└── 需要细胞类型反卷积？ → DestVI (references/spatial_deconvolution.md)

有预训练参考模型？
└── 将查询映射到参考？ → scArches (references/scarches_mapping.md)

需要RNA速率？
└── veloVI (references/rna_velocity_velovi.md)

跨技术批次效应严重？
└── sysVI (references/batch_correction_sysvi.md)
```

## 关键资源

- [scvi-tools文档](https://docs.scvi-tools.org/)
- [scvi-tools教程](https://docs.scvi-tools.org/en/stable/tutorials/index.html)
- [模型中心](https://huggingface.co/scvi-tools)
- [GitHub Issues](https://github.com/scverse/scvi-tools/issues)
