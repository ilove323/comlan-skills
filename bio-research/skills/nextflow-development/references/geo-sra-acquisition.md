# GEO/SRA数据获取

从NCBI GEO/SRA下载原始测序数据，并为nf-core流程做准备。

**使用场景：** 重新分析已发表数据集、验证研究结论或与公共队列进行比较分析。

## 目录

- [工作流程概览](#工作流程概览)
- [第1步：获取研究信息](#第1步获取研究信息)
- [第2步：查看样本分组](#第2步查看样本分组)
- [第3步：下载FASTQ文件](#第3步下载fastq文件)
- [第4步：生成样本表](#第4步生成样本表)
- [第5步：运行nf-core流程](#第5步运行nf-core流程)
- [支持的流程](#支持的流程)
- [支持的物种](#支持的物种)
- [完整示例](#完整示例)
- [故障排除](#故障排除)

---

## 工作流程概览

示例："在GSE309891（药物处理vs对照）中找出差异表达基因"

```
┌─────────────────────────────────────────────────────────────────┐
│                    GEO/SRA DATA ACQUISITION                     │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │   Fetch study info     │
                 │   • Query NCBI/SRA     │
                 │   • Get metadata       │
                 │   • Detect organism    │
                 │   • Identify data type │
                 └────────────────────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │   Present summary      │
                 │   • Organism: Human    │
                 │   • Genome: GRCh38     │
                 │   • Type: RNA-Seq      │
                 │   • Pipeline: rnaseq   │
                 │   • Samples: 12        │
                 │     (6 treated,        │
                 │      6 control)        │
                 │   • Size: ~24 GB       │
                 └────────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │  USER CONFIRMS  │◄──── 决策点
                    │  genome/pipeline│
                    └─────────────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │   Select samples       │
                 │   • Group by condition │
                 │   • Show treated/ctrl  │
                 └────────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │  USER SELECTS   │◄──── 决策点
                    │  sample subset  │
                    └─────────────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │   Download FASTQs      │
                 │   • 24 files (R1+R2)   │
                 │   • Parallel transfers │
                 │   • Auto-resume        │
                 └────────────────────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │   Generate samplesheet │
                 │   • Map SRR to files   │
                 │   • Pair R1/R2         │
                 │   • Assign conditions  │
                 └────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    NF-CORE PIPELINE EXECUTION                   │
│              (Continue with Step 1 of main workflow)            │
└─────────────────────────────────────────────────────────────────┘
```

---

## AI助手操作说明

协助用户进行GEO/SRA数据获取时：

1. **始终先获取研究信息**，向用户展示可用数据
2. **下载前需获得确认**——展示样本分组和数据量，然后使用AskUserQuestion询问要下载哪个子集
3. **根据物种和数据类型推荐合适的基因组和流程**
4. **数据准备完成后返回主SKILL.md工作流程**

确认问题示例：
```
Question: "Which sample group would you like to download?"
Options:
  - "RNA-Seq:PAIRED (42 samples, ~87 GB)"
  - "RNA-Seq:SINGLE (7 samples, ~4.5 GB)"
  - "All samples (49 samples, ~92 GB)"
```

---

## 第1步：获取研究信息

在下载前获取GEO研究的元数据。

```bash
python scripts/sra_geo_fetch.py info <GEO_ID>
```

**示例：**
```bash
python scripts/sra_geo_fetch.py info GSE110004
```

**输出包含：**
- 研究标题和摘要
- 物种（含自动推荐的基因组）
- 样本和运行数量
- 数据类型（RNA-Seq、ATAC-seq等）
- 估计下载大小
- 推荐的nf-core流程

**保存信息为JSON：**
```bash
python scripts/sra_geo_fetch.py info GSE110004 -o study_info.json
```

---

## 第2步：查看样本分组

查看按数据类型和文库结构组织的样本分组。对于含多种数据类型的研究很有用。

```bash
python scripts/sra_geo_fetch.py groups <GEO_ID>
```

**示例输出：**
```
Sample Group          Count Layout     GSM Range                    Est. Size
--------------------------------------------------------------------------------
RNA-Seq                  42 PAIRED     GSM2879618...(42 samples)      87.4 GB
RNA-Seq                   7 SINGLE     GSM2976181-GSM2976187           4.5 GB
--------------------------------------------------------------------------------
TOTAL                    49                                           91.9 GB

Available groups for --subset option:
  1. "RNA-Seq:PAIRED" - 42 samples (~87.4 GB)
  2. "RNA-Seq:SINGLE" - 7 samples (~4.5 GB)
```

**列出单个运行：**
```bash
python scripts/sra_geo_fetch.py list <GEO_ID>

# 按数据类型过滤
python scripts/sra_geo_fetch.py list GSE110004 --filter "RNA-Seq:PAIRED"
```

**决策点：** 查看样本分组。如果研究包含多种数据类型，决定要下载哪个子集。

---

## 第3步：下载FASTQ文件

从ENA下载FASTQ文件（比直接从SRA更快）。

```bash
python scripts/sra_geo_fetch.py download <GEO_ID> -o <OUTPUT_DIR>
```

**选项：**
- `-o, --output`：输出目录（必填）
- `-i, --interactive`：交互式选择要下载的样本分组
- `-s, --subset`：按数据类型过滤（例如"RNA-Seq:PAIRED"）
- `-p, --parallel`：并行下载数（默认：4）
- `-t, --timeout`：下载超时秒数（默认：600）

### 交互模式（推荐）

当研究包含多种数据类型时，使用`-i`标志进行交互式样本选择：

```bash
python scripts/sra_geo_fetch.py download GSE110004 -o ./fastq -i
```

**交互输出：**
```
============================================================
  SELECT SAMPLE GROUP TO DOWNLOAD
============================================================

  [1] RNA-Seq (paired)
      Samples: 42
      GSM: GSM2879618...(42 samples)
      Size: ~87.4 GB

  [2] RNA-Seq (single)
      Samples: 7
      GSM: GSM2976181-GSM2976187
      Size: ~4.5 GB

  [0] Download ALL (49 samples)
------------------------------------------------------------

Enter selection (0-2):
```

### 直接子集选择

也可以直接指定子集：

```bash
# 仅下载RNA-Seq双端数据
python scripts/sra_geo_fetch.py download GSE110004 -o ./fastq \
    --subset "RNA-Seq:PAIRED" --parallel 6
```

**注意：** 下载会自动跳过已存在的文件。可通过重新运行命令来恢复中断的下载。

---

## 第4步：生成样本表

创建与nf-core流程兼容的样本表。

```bash
python scripts/sra_geo_fetch.py samplesheet <GEO_ID> \
    --fastq-dir <FASTQ_DIR> \
    -o samplesheet.csv
```

**选项：**
- `-f, --fastq-dir`：包含已下载FASTQ文件的目录（必填）
- `-o, --output`：输出样本表路径（默认：samplesheet.csv）
- `-p, --pipeline`：目标流程（未指定时自动检测）

**示例：**
```bash
python scripts/sra_geo_fetch.py samplesheet GSE110004 \
    --fastq-dir ./fastq \
    -o samplesheet.csv
```

**输出：** 脚本将：
1. 以目标流程所需格式创建样本表
2. 显示推荐的基因组参考
3. 显示推荐的nf-core命令

---

## 第5步：运行nf-core流程

生成样本表后，脚本会提供推荐命令。

**示例输出：**
```
Suggested command:
   nextflow run nf-core/rnaseq \
       --input samplesheet.csv \
       --outdir results \
       --genome R64-1-1 \
       -profile docker
```

**决策点：** 审查并确认：
1. 推荐的流程是否正确？
2. 基因组参考是否与您的物种匹配？
3. 是否需要额外的流程参数？

然后返回主SKILL.md工作流程（第1步：环境检查）继续执行流程。

---

## 支持的流程

该工具根据文库策略自动检测合适的流程。标有★的流程具有完整的配置、样本表生成和文档支持。其他流程仅提供建议，需按照nf-core文档手动设置。

| 文库策略 | 推荐流程 | 支持级别 |
|---------|---------|---------|
| RNA-Seq | nf-core/rnaseq | ★ 完整 |
| ATAC-seq | nf-core/atacseq | ★ 完整 |
| WGS/WXS | nf-core/sarek | ★ 完整 |
| ChIP-seq | nf-core/chipseq | 手动 |
| Bisulfite-Seq | nf-core/methylseq | 手动 |
| miRNA-Seq | nf-core/smrnaseq | 手动 |
| Amplicon | nf-core/ampliseq | 手动 |

---

## 支持的物种

常见物种及自动推荐的基因组：

| 物种 | 基因组 | 备注 |
|------|--------|------|
| Homo sapiens | GRCh38 | 人类参考基因组 |
| Mus musculus | GRCm39 | 小鼠参考基因组 |
| Saccharomyces cerevisiae | R64-1-1 | 酵母S288C |
| Drosophila melanogaster | BDGP6 | 果蝇 |
| Caenorhabditis elegans | WBcel235 | 秀丽隐杆线虫 |
| Danio rerio | GRCz11 | 斑马鱼 |
| Arabidopsis thaliana | TAIR10 | 拟南芥 |
| Rattus norvegicus | Rnor_6.0 | 大鼠 |

完整列表请参见`scripts/config/genomes.yaml`。

---

## 完整示例

重新分析GSE110004（酵母RNA-seq）：

```bash
# 1. 获取研究信息和样本分组
python scripts/sra_geo_fetch.py info GSE110004

# 2. 交互式选择下载
python scripts/sra_geo_fetch.py download GSE110004 -o ./fastq -i
# 选择[1]下载RNA-Seq双端样本

# 3. 生成样本表
python scripts/sra_geo_fetch.py samplesheet GSE110004 \
    --fastq-dir ./fastq \
    -o samplesheet.csv

# 4. 运行nf-core/rnaseq（继续主SKILL.md工作流程）
nextflow run nf-core/rnaseq \
    --input samplesheet.csv \
    --outdir results \
    --genome R64-1-1 \
    -profile docker
```

### 替代方案：非交互式下载

```bash
# 先查看样本分组
python scripts/sra_geo_fetch.py groups GSE110004

# 直接下载特定子集
python scripts/sra_geo_fetch.py download GSE110004 \
    --subset "RNA-Seq:PAIRED" \
    -o ./fastq \
    --parallel 4
```

---

## 故障排除

### ENA下载失败
如果ENA下载失败，可能需要直接从SRA获取数据：

```bash
# 创建SRA工具环境
conda create -n sra_tools -c bioconda sra-tools

# 使用prefetch + fasterq-dump下载
conda run -n sra_tools prefetch SRR6357070
conda run -n sra_tools fasterq-dump SRR6357070 -O ./fastq
```

### 未找到SRA运行
某些GEO数据集只有处理后的数据，没有原始测序reads。检查方法：
```bash
python scripts/sra_geo_fetch.py info <GEO_ID>
```
如果"Runs: 0"，则该数据集可能在SRA中没有原始数据。

### SuperSeries支持
GEO SuperSeries（包含多个SubSeries的合集）会被自动处理。该工具将：
1. 检测GEO ID是否为SuperSeries
2. 找到关联的BioProject登录号
3. 从BioProject获取所有SRA运行

示例：GSE110004是一个SuperSeries，关联到BioProject PRJNA432544。

### 基因组未识别
如果物种不在基因组映射中，手动指定基因组：
```bash
# 检查可用的iGenomes
python scripts/manage_genomes.py list

# 或向nf-core提供自定义参考文件
nextflow run nf-core/rnaseq --fasta /path/to/genome.fa --gtf /path/to/genes.gtf
```

---

## 系统要求

- Python 3.8+
- `requests`库（可选但推荐）
- `pyyaml`库（可选，用于基因组配置）
- 可访问NCBI和ENA的网络

安装可选依赖：
```bash
pip install requests pyyaml
```
