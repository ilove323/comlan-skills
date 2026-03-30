# nf-core/sarek

**版本：** 3.7.1

**官方文档：** https://nf-co.re/sarek/3.7.1/
**GitHub：** https://github.com/nf-core/sarek

> **注意：** 更新到新版本时，请查阅[发布页面](https://github.com/nf-core/sarek/releases)了解重大变更，并更新以下命令中的版本号。

## 目录
- [测试命令](#测试命令)
- [样本表格式](#样本表格式)
- [变异检测模式](#变异检测模式)
- [参数](#参数)
- [输出文件](#输出文件)

## 测试命令

```bash
nextflow run nf-core/sarek -r 3.7.1 -profile test,docker --outdir test_sarek
```

预期：约20分钟，生成比对后的BAM文件和变异检测结果。

## 样本表格式

### 来自FASTQ
```csv
patient,sample,lane,fastq_1,fastq_2
patient1,tumor,L001,/path/to/tumor_L001_R1.fq.gz,/path/to/tumor_L001_R2.fq.gz
patient1,tumor,L002,/path/to/tumor_L002_R1.fq.gz,/path/to/tumor_L002_R2.fq.gz
patient1,normal,L001,/path/to/normal_R1.fq.gz,/path/to/normal_R2.fq.gz
```

### 来自BAM/CRAM
```csv
patient,sample,bam,bai
patient1,tumor,/path/to/tumor.bam,/path/to/tumor.bam.bai
patient1,normal,/path/to/normal.bam,/path/to/normal.bam.bai
```

### 含肿瘤/正常状态
```csv
patient,sample,lane,fastq_1,fastq_2,status
patient1,tumor,L001,tumor_R1.fq.gz,tumor_R2.fq.gz,1
patient1,normal,L001,normal_R1.fq.gz,normal_R2.fq.gz,0
```

`status`：0 = 正常，1 = 肿瘤

## 变异检测模式

### 胚系变异（单样本）
```bash
nextflow run nf-core/sarek -r 3.7.1 -profile docker \
    --input samplesheet.csv --outdir results --genome GRCh38 \
    --tools haplotypecaller,snpeff
```

### 体细胞变异（肿瘤-正常配对）
```bash
nextflow run nf-core/sarek -r 3.7.1 -profile docker \
    --input samplesheet.csv --outdir results --genome GRCh38 \
    --tools mutect2,strelka,snpeff
```

### 全外显子组测序（WES）
```bash
nextflow run nf-core/sarek -r 3.7.1 -profile docker \
    --input samplesheet.csv --outdir results --genome GRCh38 \
    --wes --intervals /path/to/targets.bed \
    --tools haplotypecaller,snpeff
```

### 联合胚系变异（队列）
```bash
--tools haplotypecaller --joint_germline
```

## 参数

### 可用工具

**胚系变异检测：**
- `haplotypecaller`：GATK HaplotypeCaller
- `freebayes`：FreeBayes
- `deepvariant`：DeepVariant（可选GPU）
- `strelka`：Strelka2胚系模式

**体细胞变异检测：**
- `mutect2`：GATK Mutect2
- `strelka`：Strelka2体细胞模式
- `manta`：结构变异

**拷贝数变异检测：**
- `ascat`：拷贝数变异
- `controlfreec`：CNV检测
- `tiddit`：SV检测

**注释：**
- `snpeff`：功能注释
- `vep`：变异效应预测器

### 关键参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--tools` | - | 逗号分隔的工具列表 |
| `--genome` | - | `GRCh38`、`GRCh37` |
| `--wes` | false | 外显子组模式（需要`--intervals`） |
| `--intervals` | - | 靶向区域BED文件 |
| `--joint_germline` | false | 队列联合检测 |
| `--skip_bqsr` | false | 跳过碱基质量校正 |

## 输出文件

```
results/
├── preprocessing/
│   └── recalibrated/           # 可供分析的BAM文件
│       └── *.recal.bam
├── variant_calling/
│   ├── haplotypecaller/        # 胚系VCF文件
│   ├── mutect2/                # 体细胞VCF文件（已过滤）
│   └── strelka/
├── annotation/
│   └── snpeff/                 # 已注释的VCF文件
└── multiqc/
```

## 故障排除

**BQSR失败**：检查基因组的已知变异位点。对非标准参考使用`--skip_bqsr`跳过。

**Mutect2无变异结果**：验证样本表中的肿瘤/正常配对（检查`status`列）。

**WGS内存不足**：使用`--max_memory '128.GB'`。

**DeepVariant GPU问题**：确保配置了NVIDIA Docker运行时。

## 更多信息

- **完整参数列表：** https://nf-co.re/sarek/3.7.1/parameters/
- **输出文档：** https://nf-co.re/sarek/3.7.1/docs/output/
- **使用文档：** https://nf-co.re/sarek/3.7.1/docs/usage/
