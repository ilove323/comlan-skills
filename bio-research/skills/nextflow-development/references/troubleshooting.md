# 故障排除

nf-core流程常见问题的快速修复方案。

## 目录
- [退出代码](#退出代码)
- [HPC/Singularity问题](#hpcsingularity问题)
- [流程失败](#流程失败)
- [RNA-seq特定问题](#rna-seq特定问题)
- [Sarek特定问题](#sarek特定问题)
- [ATAC-seq特定问题](#atac-seq特定问题)
- [资源管理](#资源管理)
- [获取帮助](#获取帮助)

## 退出代码

表示资源问题的常见退出代码（参见[nf-core文档](https://nf-co.re/docs/usage/troubleshooting/crash_halfway)）：

| 代码 | 原因 | 修复方法 |
|------|------|---------|
| 137 | 内存不足 | `--max_memory '32.GB'`或`'64.GB'`（用于WGS） |
| 143 | 内存不足 | `--max_memory '32.GB'`或`'64.GB'`（用于WGS） |
| 104, 134, 139, 247 | 内存不足 | 增加`--max_memory` |
| 1 | 一般错误 | 检查`.nextflow.log`获取详情 |

大多数流程在失败前会自动以2倍然后3倍资源重试。

## HPC/Singularity问题

### Singularity缓存问题
```bash
export NXF_SINGULARITY_CACHEDIR="$HOME/.singularity/cache"
mkdir -p $NXF_SINGULARITY_CACHEDIR
```

### 使用Singularity代替Docker
在没有Docker的HPC系统上，使用Singularity：
```bash
nextflow run nf-core/<pipeline> -profile singularity ...
```

> **注意**：关于基本环境设置（Docker、Nextflow、Java安装），请参阅SKILL.md第1步中的内嵌说明。

## 流程失败

### 容器拉取失败
- 检查网络连接
- 尝试：使用`-profile singularity`代替docker
- 离线使用：`nf-core download <pipeline> -r <version>`

### "No such file"错误
- 在样本表中使用**绝对路径**
- 验证文件存在：`ls /path/to/file`

### 断点续跑不起效
```bash
# 检查工作目录是否存在
ls -la work/

# 强制重新开始（会清除缓存）
rm -rf work/ .nextflow*
nextflow run nf-core/<pipeline> ...
```

## RNA-seq特定问题

### STAR索引构建失败
- 增加内存：`--max_memory '64.GB'`
- 或提供预构建索引：`--star_index /path/to/star/`

### 比对率低
- 验证基因组与物种匹配
- 检查FastQC报告中的接头污染
- 尝试不同比对器：`--aligner hisat2`

### 链特异性检测失败
- 明确指定：`--strandedness reverse`
- 常见值：`forward`、`reverse`、`unstranded`

## Sarek特定问题

### BQSR失败
- 检查基因组的已知变异位点
- 对非标准参考跳过：`--skip_bqsr`

### Mutect2无变异结果
- 验证肿瘤/正常配对
- 检查样本表`status`列：0=正常，1=肿瘤

### WGS内存不足
```bash
--max_memory '128.GB' --max_cpus 16
```

### DeepVariant GPU问题
- 确保配置了NVIDIA Docker运行时
- 或使用CPU模式（较慢）

## ATAC-seq特定问题

### FRiP评分低
- 检查`plotFingerprint/`中的文库复杂度
- 可能表示过度转座

### peak数量少
- 降低阈值：`--macs_qvalue 0.1`
- 使用宽peak：`--narrow_peak false`

### 重复率高
- 低投入量样本的正常现象
- 流程默认会去除重复
- 考虑更深的测序深度

## 资源管理

### 设置资源限制
```bash
--max_cpus 8 --max_memory '32.GB' --max_time '24.h'
```

### 检查可用资源
```bash
# CPU核数
nproc

# 内存
free -h

# 磁盘空间
df -h .
```

## 获取帮助

1. 检查`.nextflow.log`中的错误详情
2. 搜索nf-core Slack：https://nf-co.re/join
3. 在GitHub上提交issue：https://github.com/nf-core/<pipeline>/issues
