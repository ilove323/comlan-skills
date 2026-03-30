# nf-core/rnaseq

**版本：** 3.22.2

**官方文档：** https://nf-co.re/rnaseq/3.22.2/
**GitHub：** https://github.com/nf-core/rnaseq

> **注意：** 更新到新版本时，请查阅[发布页面](https://github.com/nf-core/rnaseq/releases)了解重大变更，并更新以下命令中的版本号。

## 目录
- [测试命令](#测试命令)
- [样本表格式](#样本表格式)
- [参数](#参数)
- [输出文件](#输出文件)
- [下游分析](#下游分析)

## 测试命令

```bash
nextflow run nf-core/rnaseq -r 3.22.2 -profile test,docker --outdir test_rnaseq
```

预期：约15分钟，生成`multiqc/multiqc_report.html`。

## 样本表格式

```csv
sample,fastq_1,fastq_2,strandedness
CONTROL_REP1,/path/to/ctrl1_R1.fq.gz,/path/to/ctrl1_R2.fq.gz,auto
CONTROL_REP2,/path/to/ctrl2_R1.fq.gz,/path/to/ctrl2_R2.fq.gz,auto
TREATMENT_REP1,/path/to/treat1_R1.fq.gz,/path/to/treat1_R2.fq.gz,auto
```

| 列名 | 必填 | 说明 |
|------|------|------|
| sample | 是 | 字母数字字符，允许下划线 |
| fastq_1 | 是 | R1文件的绝对路径 |
| fastq_2 | 否 | R2文件的绝对路径（单端测序留空） |
| strandedness | 是 | `auto`、`forward`、`reverse`、`unstranded` |

**链特异性指南：**
- `auto`：从数据推断（推荐）
- `forward`：TruSeq Stranded、dUTP方案
- `reverse`：连接酶方案
- `unstranded`：非链特异性方案

## 参数

### 最简运行
```bash
nextflow run nf-core/rnaseq -r 3.22.2 -profile docker \
    --input samplesheet.csv --outdir results --genome GRCh38
```

### 常用参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--aligner` | `star_salmon` | 选项：`star_salmon`、`star_rsem`、`hisat2` |
| `--genome` | - | `GRCh38`、`GRCh37`、`mm10`、`BDGP6` |
| `--pseudo_aligner` | - | 设为`salmon`仅进行伪比对 |
| `--skip_trimming` | false | 跳过接头修剪 |
| `--skip_alignment` | false | 仅进行伪比对 |

### 自定义参考
```bash
--fasta /path/to/genome.fa \
--gtf /path/to/annotation.gtf \
--star_index /path/to/star/  # 可选，缺失时自动构建
```

## 输出文件

```
results/
├── star_salmon/
│   ├── salmon.merged.gene_counts.tsv    # 用于DESeq2的原始计数
│   ├── salmon.merged.gene_tpm.tsv       # TPM值
│   └── *.bam                            # 比对结果
├── multiqc/
│   └── multiqc_report.html              # QC汇总报告
└── pipeline_info/
```

**关键输出：**
- `salmon.merged.gene_counts.tsv`：DESeq2/edgeR的输入
- `salmon.merged.gene_tpm.tsv`：标准化表达量

## 下游分析

```r
library(DESeq2)
counts <- read.delim("salmon.merged.gene_counts.tsv", row.names=1)
coldata <- data.frame(
    condition = factor(c("control", "control", "treatment", "treatment"))
)
dds <- DESeqDataSetFromMatrix(
    countData = round(counts),
    colData = coldata,
    design = ~ condition
)
dds <- DESeq(dds)
res <- results(dds, contrast = c("condition", "treatment", "control"))
```

## 故障排除

**STAR索引构建失败**：使用`--max_memory '64.GB'`增加内存，或提供预构建的`--star_index`。

**比对率低**：验证基因组与物种匹配；检查FastQC报告中的接头污染。

**链特异性检测失败**：使用`--strandedness reverse`明确指定。

## 更多信息

- **完整参数列表：** https://nf-co.re/rnaseq/3.22.2/parameters/
- **输出文档：** https://nf-co.re/rnaseq/3.22.2/docs/output/
- **使用文档：** https://nf-co.re/rnaseq/3.22.2/docs/usage/
