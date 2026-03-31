---
name: nextflow-development
description: 在测序数据上运行nf-core生物信息学流程（rnaseq、sarek、atacseq）。当分析RNA-seq、WGS/WES或ATAC-seq数据时使用——无论是本地FASTQ文件还是来自GEO/SRA的公共数据集。触发词包括：nf-core、Nextflow、FASTQ分析、变异检测、基因表达、差异表达、GEO重分析、GSE/GSM/SRR登录号或样本表创建。触发词：Nextflow开发、流程开发、nextflow-development
---

# nf-core流程部署

在本地或公共测序数据上运行nf-core生物信息学流程。

**目标用户：** 没有专业生物信息学培训但需要运行大规模组学分析的实验室科学家和研究人员——包括差异表达、变异检测或染色质可及性分析。

## 工作流程检查清单

```
- [ ] 第0步：获取数据（如来自GEO/SRA）
- [ ] 第1步：环境检查（必须通过）
- [ ] 第2步：选择流程（与用户确认）
- [ ] 第3步：运行测试配置文件（必须通过）
- [ ] 第4步：创建样本表
- [ ] 第5步：配置并运行（与用户确认参考基因组）
- [ ] 第6步：验证输出
```

---

## 第0步：获取数据（仅限GEO/SRA）

**如果用户有本地FASTQ文件，请跳过此步骤。**

对于公共数据集，首先从GEO/SRA获取数据。完整工作流程请参阅 [references/geo-sra-acquisition.md](references/geo-sra-acquisition.md)。

**快速入门：**

```bash
# 1. 获取研究信息
python scripts/sra_geo_fetch.py info GSE110004

# 2. 下载（交互模式）
python scripts/sra_geo_fetch.py download GSE110004 -o ./fastq -i

# 3. 生成样本表
python scripts/sra_geo_fetch.py samplesheet GSE110004 --fastq-dir ./fastq -o samplesheet.csv
```

**决策节点：** 获取研究信息后，与用户确认：
- 要下载哪些样本子集（如果有多种数据类型）
- 建议的参考基因组和流程

然后继续第1步。

---

## 第1步：环境检查

**首先运行。没有通过环境检查，流程将无法运行。**

```bash
python scripts/check_environment.py
```

所有关键检查必须通过。如有任何失败，提供修复说明：

### Docker问题

| 问题 | 修复方法 |
|------|----------|
| 未安装 | 从 https://docs.docker.com/get-docker/ 安装 |
| 权限被拒绝 | `sudo usermod -aG docker $USER` 然后重新登录 |
| 守护进程未运行 | `sudo systemctl start docker` |

### Nextflow问题

| 问题 | 修复方法 |
|------|----------|
| 未安装 | `curl -s https://get.nextflow.io \| bash && mv nextflow ~/bin/` |
| 版本 < 23.04 | `nextflow self-update` |

### Java问题

| 问题 | 修复方法 |
|------|----------|
| 未安装 / < 11 | `sudo apt install openjdk-11-jdk` |

**在所有检查通过之前不要继续。** 对于HPC/Singularity，请参阅 [references/troubleshooting.md](references/troubleshooting.md)。

---

## 第2步：选择流程

**决策节点：继续之前请与用户确认。**

| 数据类型 | 流程 | 版本 | 目标 |
|----------|------|------|------|
| RNA-seq | `rnaseq` | 3.22.2 | 基因表达 |
| WGS/WES | `sarek` | 3.7.1 | 变异检测 |
| ATAC-seq | `atacseq` | 2.1.2 | 染色质可及性 |

从数据自动检测：
```bash
python scripts/detect_data_type.py /path/to/data
```

各流程详细信息请参阅：
- [references/pipelines/rnaseq.md](references/pipelines/rnaseq.md)
- [references/pipelines/sarek.md](references/pipelines/sarek.md)
- [references/pipelines/atacseq.md](references/pipelines/atacseq.md)

---

## 第3步：运行测试配置文件

**用小数据验证环境。在真实数据运行之前必须通过。**

```bash
nextflow run nf-core/<pipeline> -r <version> -profile test,docker --outdir test_output
```

| 流程 | 命令 |
|------|------|
| rnaseq | `nextflow run nf-core/rnaseq -r 3.22.2 -profile test,docker --outdir test_rnaseq` |
| sarek | `nextflow run nf-core/sarek -r 3.7.1 -profile test,docker --outdir test_sarek` |
| atacseq | `nextflow run nf-core/atacseq -r 2.1.2 -profile test,docker --outdir test_atacseq` |

验证：
```bash
ls test_output/multiqc/multiqc_report.html
grep "Pipeline completed successfully" .nextflow.log
```

如果测试失败，请参阅 [references/troubleshooting.md](references/troubleshooting.md)。

---

## 第4步：创建样本表

### 自动生成

```bash
python scripts/generate_samplesheet.py /path/to/data <pipeline> -o samplesheet.csv
```

脚本会：
- 发现FASTQ/BAM/CRAM文件
- 配对R1/R2读段
- 推断样本元数据
- 写入前进行验证

**对于sarek：** 如果无法自动检测，脚本会提示输入肿瘤/正常状态。

### 验证现有样本表

```bash
python scripts/generate_samplesheet.py --validate samplesheet.csv <pipeline>
```

### 样本表格式

**rnaseq：**
```csv
sample,fastq_1,fastq_2,strandedness
SAMPLE1,/abs/path/R1.fq.gz,/abs/path/R2.fq.gz,auto
```

**sarek：**
```csv
patient,sample,lane,fastq_1,fastq_2,status
patient1,tumor,L001,/abs/path/tumor_R1.fq.gz,/abs/path/tumor_R2.fq.gz,1
patient1,normal,L001,/abs/path/normal_R1.fq.gz,/abs/path/normal_R2.fq.gz,0
```

**atacseq：**
```csv
sample,fastq_1,fastq_2,replicate
CONTROL,/abs/path/ctrl_R1.fq.gz,/abs/path/ctrl_R2.fq.gz,1
```

---

## 第5步：配置并运行

### 第5a步：检查参考基因组可用性

```bash
python scripts/manage_genomes.py check <genome>
# 如果未安装：
python scripts/manage_genomes.py download <genome>
```

常用参考基因组：GRCh38（人类）、GRCh37（旧版）、GRCm39（小鼠）、R64-1-1（酵母）、BDGP6（果蝇）

### 第5b步：决策节点

**决策节点：请与用户确认：**

1. **参考基因组：** 使用哪个参考
2. **流程特定选项：**
   - **rnaseq：** 对齐器（推荐star_salmon，内存不足时使用hisat2）
   - **sarek：** 工具（胚系变异用haplotypecaller，体细胞变异用mutect2）
   - **atacseq：** read_length（50、75、100或150）

### 第5c步：运行流程

```bash
nextflow run nf-core/<pipeline> \
    -r <version> \
    -profile docker \
    --input samplesheet.csv \
    --outdir results \
    --genome <genome> \
    -resume
```

**关键参数：**
- `-r`：固定版本
- `-profile docker`：使用Docker（HPC使用 `singularity`）
- `--genome`：iGenomes键
- `-resume`：从检查点继续

**资源限制（如有需要）：**
```bash
--max_cpus 8 --max_memory '32.GB' --max_time '24.h'
```

---

## 第6步：验证输出

### 检查完成情况

```bash
ls results/multiqc/multiqc_report.html
grep "Pipeline completed successfully" .nextflow.log
```

### 各流程关键输出

**rnaseq：**
- `results/star_salmon/salmon.merged.gene_counts.tsv` - 基因计数
- `results/star_salmon/salmon.merged.gene_tpm.tsv` - TPM值

**sarek：**
- `results/variant_calling/*/` - VCF文件
- `results/preprocessing/recalibrated/` - BAM文件

**atacseq：**
- `results/macs2/narrowPeak/` - Peak调用
- `results/bwa/mergedLibrary/bigwig/` - 覆盖度轨迹

---

## 快速参考

常见退出代码和修复方法，请参阅 [references/troubleshooting.md](references/troubleshooting.md)。

### 恢复失败的运行

```bash
nextflow run nf-core/<pipeline> -resume
```

---

## 参考资料

- [references/geo-sra-acquisition.md](references/geo-sra-acquisition.md) - 下载公共GEO/SRA数据
- [references/troubleshooting.md](references/troubleshooting.md) - 常见问题和修复方法
- [references/installation.md](references/installation.md) - 环境配置
- [references/pipelines/rnaseq.md](references/pipelines/rnaseq.md) - RNA-seq流程详情
- [references/pipelines/sarek.md](references/pipelines/sarek.md) - 变异检测详情
- [references/pipelines/atacseq.md](references/pipelines/atacseq.md) - ATAC-seq详情

---

## 免责声明

本技能作为示例原型，演示如何将nf-core生物信息学流程集成到OpenClaw中进行自动化分析。当前实现支持三个流程（rnaseq、sarek和atacseq），作为基础框架，使社区能够扩展支持完整的nf-core流程集。

本技能用于教育和研究目的，未经针对您具体用例的适当验证，不应视为生产就绪。用户有责任确保其计算环境满足流程要求，并验证分析结果。

本工具不保证生物信息学输出的准确性，用户应遵循计算分析验证的标准实践。本集成未获得nf-core社区的官方认可或隶属关系。

## 致谢

发表结果时，请引用相应流程。引用信息可在各nf-core代码库的CITATIONS.md文件中找到（例如 https://github.com/nf-core/rnaseq/blob/3.22.2/CITATIONS.md）。

## 许可证

- **nf-core流程：** MIT许可证（https://nf-co.re/about）
- **Nextflow：** Apache License, Version 2.0（https://www.nextflow.io/about-us.html）
- **NCBI SRA Toolkit：** Public Domain（https://github.com/ncbi/sra-tools/blob/master/LICENSE）
