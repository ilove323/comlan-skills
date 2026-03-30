---
name: single-cell-rna-qc
description: 使用基于MAD过滤的scverse最佳实践对单细胞RNA-seq数据（.h5ad或.h5文件）进行质量控制，并生成全面可视化图表。当用户请求QC分析、过滤低质量细胞、评估数据质量或遵循scverse/scanpy单细胞分析最佳实践时使用。触发词：单细胞RNA质控、scRNA-seq QC、single-cell-rna-qc
---

# 单细胞RNA-seq质量控制

遵循scverse最佳实践的自动化单细胞RNA-seq数据QC工作流程。

## 何时使用本技能

以下情况使用本技能：
- 用户请求单细胞RNA-seq数据的质量控制或QC
- 用户希望过滤低质量细胞或评估数据质量
- 用户需要QC可视化图表或指标
- 用户要求遵循scverse/scanpy最佳实践
- 用户请求基于MAD的过滤或异常值检测

**支持的输入格式：**
- `.h5ad` 文件（来自scanpy/Python工作流的AnnData格式）
- `.h5` 文件（10X Genomics Cell Ranger输出）

**默认建议**：使用方案一（完整流程），除非用户有特定的自定义需求或明确要求非标准过滤逻辑。

## 方案一：完整QC流程（推荐用于标准工作流）

对于遵循scverse最佳实践的标准QC，请使用便捷脚本 `scripts/qc_analysis.py`：

```bash
python3 scripts/qc_analysis.py input.h5ad
# 或对于10X Genomics .h5文件：
python3 scripts/qc_analysis.py raw_feature_bc_matrix.h5
```

脚本会自动检测文件格式并进行相应加载。

**适用场景：**
- 具有可调阈值的标准QC工作流（所有细胞以相同方式过滤）
- 批量处理多个数据集
- 快速探索性分析
- 用户希望"开箱即用"的解决方案

**依赖包：** anndata, scanpy, scipy, matplotlib, seaborn, numpy

**参数：**

使用命令行参数自定义过滤阈值和基因模式：
- `--output-dir` - 输出目录
- `--mad-counts`, `--mad-genes`, `--mad-mt` - 计数/基因/线粒体%的MAD阈值
- `--mt-threshold` - 线粒体%硬截断值
- `--min-cells` - 基因过滤阈值
- `--mt-pattern`, `--ribo-pattern`, `--hb-pattern` - 不同物种的基因名称模式

使用 `--help` 查看当前默认值。

**输出文件：**

所有文件默认保存至 `<输入文件名>_qc_results/` 目录（或由 `--output-dir` 指定的目录）：
- `qc_metrics_before_filtering.png` - 过滤前可视化
- `qc_filtering_thresholds.png` - 基于MAD的阈值叠加图
- `qc_metrics_after_filtering.png` - 过滤后质量指标
- `<输入文件名>_filtered.h5ad` - 可用于下游分析的干净过滤数据集
- `<输入文件名>_with_qc.h5ad` - 保留QC注释的原始数据

如需复制输出供用户访问，请复制单个文件（而非整个目录），以便用户直接预览。

### 工作流程步骤

脚本执行以下步骤：

1. **计算QC指标** - 计数深度、基因检测数、线粒体/核糖体/血红蛋白含量
2. **应用基于MAD的过滤** - 使用MAD阈值进行宽松的异常值检测（计数/基因/线粒体%）
3. **过滤基因** - 移除在少量细胞中检测到的基因
4. **生成可视化** - 包含阈值叠加的过滤前后综合图表

## 方案二：模块化构建块（用于自定义工作流）

对于自定义分析工作流或非标准需求，请使用 `scripts/qc_core.py` 和 `scripts/qc_plotting.py` 中的模块化工具函数：

```python
# 从scripts/目录运行，或在需要时将scripts/添加到sys.path
import anndata as ad
from qc_core import calculate_qc_metrics, detect_outliers_mad, filter_cells
from qc_plotting import plot_qc_distributions  # 仅在需要可视化时使用

adata = ad.read_h5ad('input.h5ad')
calculate_qc_metrics(adata, inplace=True)
# ... 此处放置自定义分析逻辑
```

**适用场景：**
- 需要不同的工作流（跳过步骤、更改顺序、对子集应用不同阈值）
- 条件逻辑（如对神经元与其他细胞应用不同过滤方式）
- 部分执行（仅指标/可视化，无过滤）
- 与更大流程中的其他分析步骤集成
- 命令行参数不支持的自定义过滤条件

**可用工具函数：**

来自 `qc_core.py`（核心QC操作）：
- `calculate_qc_metrics(adata, mt_pattern, ribo_pattern, hb_pattern, inplace=True)` - 计算QC指标并注释adata
- `detect_outliers_mad(adata, metric, n_mads, verbose=True)` - 基于MAD的异常值检测，返回布尔掩码
- `apply_hard_threshold(adata, metric, threshold, operator='>', verbose=True)` - 应用硬截断，返回布尔掩码
- `filter_cells(adata, mask, inplace=False)` - 应用布尔掩码过滤细胞
- `filter_genes(adata, min_cells=20, min_counts=None, inplace=True)` - 按检测数过滤基因
- `print_qc_summary(adata, label='')` - 打印汇总统计

来自 `qc_plotting.py`（可视化）：
- `plot_qc_distributions(adata, output_path, title)` - 生成综合QC图
- `plot_filtering_thresholds(adata, outlier_masks, thresholds, output_path)` - 可视化过滤阈值
- `plot_qc_after_filtering(adata, output_path)` - 生成过滤后图表

**自定义工作流示例：**

**示例1：仅计算指标和可视化，暂不过滤**
```python
adata = ad.read_h5ad('input.h5ad')
calculate_qc_metrics(adata, inplace=True)
plot_qc_distributions(adata, 'qc_before.png', title='初始QC')
print_qc_summary(adata, label='过滤前')
```

**示例2：仅应用线粒体%过滤，其他指标保持宽松**
```python
adata = ad.read_h5ad('input.h5ad')
calculate_qc_metrics(adata, inplace=True)

# 仅过滤高线粒体%的细胞
high_mt = apply_hard_threshold(adata, 'pct_counts_mt', 10, operator='>')
adata_filtered = filter_cells(adata, ~high_mt)
adata_filtered.write('filtered.h5ad')
```

**示例3：对不同子集应用不同阈值**
```python
adata = ad.read_h5ad('input.h5ad')
calculate_qc_metrics(adata, inplace=True)

# 应用特定细胞类型的QC（假设已有cell_type元数据）
neurons = adata.obs['cell_type'] == 'neuron'
other_cells = ~neurons

# 神经元可接受更高的线粒体%，其他细胞使用更严格的阈值
neuron_qc = apply_hard_threshold(adata[neurons], 'pct_counts_mt', 15, operator='>')
other_qc = apply_hard_threshold(adata[other_cells], 'pct_counts_mt', 8, operator='>')
```

## 最佳实践

1. **过滤保持宽松** - 默认阈值有意保留大多数细胞，避免丢失稀有细胞群
2. **检查可视化** - 始终在过滤前后查看图表，确保过滤在生物学上合理
3. **考虑数据集特异性因素** - 某些组织的线粒体含量自然较高（如神经元、心肌细胞）
4. **检查基因注释** - 线粒体基因前缀因物种而异（小鼠用mt-，人类用MT-）
5. **必要时迭代** - QC参数可能需要根据具体实验或组织类型进行调整

## 参考资料

有关详细的QC方法、参数说明和故障排查指南，请参阅 `references/scverse_qc_guidelines.md`。该参考资料提供：
- 每个QC指标的详细说明及重要性
- 基于MAD阈值的原理及其优于固定截断值的原因
- QC可视化图表解读指南（直方图、小提琴图、散点图）
- 基因注释的物种特异性注意事项
- 何时以及如何调整过滤参数
- 高级QC考虑因素（背景RNA校正、双细胞检测）

当用户需要深入理解方法论或排查QC问题时，加载此参考资料。

## QC后续步骤

典型的下游分析步骤：
- 背景RNA校正（SoupX、CellBender）
- 双细胞检测（scDblFinder）
- 标准化（log-normalize、scran）
- 特征选择和降维
- 聚类和细胞类型注释
