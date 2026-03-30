# nf-core/atacseq

**版本：** 2.1.2

**官方文档：** https://nf-co.re/atacseq/2.1.2/
**GitHub：** https://github.com/nf-core/atacseq

> **注意：** 更新到新版本时，请查阅[发布页面](https://github.com/nf-core/atacseq/releases)了解重大变更，并更新以下命令中的版本号。

## 目录
- [测试命令](#测试命令)
- [样本表格式](#样本表格式)
- [参数](#参数)
- [输出文件](#输出文件)
- [质量指标](#质量指标)

## 测试命令

```bash
nextflow run nf-core/atacseq -r 2.1.2 -profile test,docker --outdir test_atacseq
```

预期：约15分钟，生成peaks文件和BigWig轨迹。

## 样本表格式

```csv
sample,fastq_1,fastq_2,replicate
CONTROL,/path/to/ctrl_rep1_R1.fq.gz,/path/to/ctrl_rep1_R2.fq.gz,1
CONTROL,/path/to/ctrl_rep2_R1.fq.gz,/path/to/ctrl_rep2_R2.fq.gz,2
TREATMENT,/path/to/treat_rep1_R1.fq.gz,/path/to/treat_rep1_R2.fq.gz,1
TREATMENT,/path/to/treat_rep2_R1.fq.gz,/path/to/treat_rep2_R2.fq.gz,2
```

| 列名 | 必填 | 说明 |
|------|------|------|
| sample | 是 | 条件/组别标识符 |
| fastq_1 | 是 | R1文件的绝对路径 |
| fastq_2 | 是 | R2文件的绝对路径（需要双端测序） |
| replicate | 是 | 重复编号（整数） |

### 差异分析设计文件
```csv
sample,condition
CONTROL,control
TREATMENT,treatment
```

与`--deseq2_design design.csv`配合使用。

## 参数

### 最简运行
```bash
nextflow run nf-core/atacseq -r 2.1.2 -profile docker \
    --input samplesheet.csv --outdir results --genome GRCh38 --read_length 50
```

### 常用参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--genome` | - | `GRCh38`、`GRCh37`、`mm10` |
| `--read_length` | 50 | 用于MACS2优化的reads长度 |
| `--narrow_peak` | true | 窄peak（false为宽peak） |
| `--mito_name` | chrM | 线粒体染色体名称 |
| `--keep_mito` | false | 保留线粒体reads |
| `--min_reps_consensus` | 1 | 共识peak的最少重复数 |

### 差异可及性分析
```bash
--deseq2_design design.csv
```

## 输出文件

```
results/
├── bwa/mergedLibrary/
│   ├── *.mLb.mkD.sorted.bam     # 已过滤、去重的比对结果
│   └── bigwig/
│       └── *.bigWig             # 覆盖度轨迹
├── macs2/narrowPeak/
│   ├── *.narrowPeak             # Peak检测结果
│   └── consensus/
│       └── consensus_peaks.bed  # 重复间的合并peak
├── deeptools/
│   ├── plotFingerprint/         # 文库复杂度
│   └── plotProfile/             # TSS富集
├── deseq2/                      # 提供--deseq2_design时生成
└── multiqc/
```

**关键输出：**
- `*.mLb.mkD.sorted.bam`：可供分析的比对文件
- `*.narrowPeak`：MACS2 peak检测结果（BED格式）
- `consensus_peaks.bed`：重复间的共识peak
- `*.bigWig`：基因组浏览器轨迹

## 质量指标

| 指标 | 良好 | 可接受 | 较差 |
|------|------|--------|------|
| 比对率 | >80% | 60-80% | <60% |
| 线粒体比例 | <20% | 20-40% | >40% |
| 重复率 | <30% | 30-50% | >50% |
| FRiP | >30% | 15-30% | <15% |
| TSS富集 | >6 | 4-6 | <4 |

**片段大小**：应显示核小体周期性（约50bp无核小体区，约200bp单核小体区）。

## 下游分析

```r
library(ChIPseeker)
library(GenomicRanges)
peaks <- import("consensus_peaks.bed")
peakAnno <- annotatePeak(peaks, TxDb = TxDb.Hsapiens.UCSC.hg38.knownGene)
```

**基序分析：**
```bash
findMotifsGenome.pl consensus_peaks.bed hg38 motifs/ -size 200
```

## 故障排除

**FRiP评分低**：检查`plotFingerprint/`中的文库复杂度。可能表示过度转座。

**peak数量少**：使用`--macs_qvalue 0.1`降低阈值，或使用`--narrow_peak false`检测更宽的peak。

**重复率高**：低投入量样本的正常现象；流程默认会去除重复。

## 更多信息

- **完整参数列表：** https://nf-co.re/atacseq/2.1.2/parameters/
- **输出文档：** https://nf-co.re/atacseq/2.1.2/docs/output/
- **使用文档：** https://nf-co.re/atacseq/2.1.2/docs/usage/
