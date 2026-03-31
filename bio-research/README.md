# 生物研究插件

连接临床前研究工具和数据库（文献检索、基因组学分析、靶点优先级排序），加速生命科学早期研发。可与 [OpenClaw](https://openclaw.ai) 配合使用，或直接安装在Claude Code中。

本插件将10个MCP服务器集成和5个分析技能整合为一个生命科学研究人员专用套件。

## 包含内容

### MCP服务器（数据源与工具）

> 如果看到不熟悉的占位符或需要检查已连接的工具，请参阅[CONNECTORS.md](CONNECTORS.md)。

| 提供商 | 功能 | 类别/占位符 |
|--------|------|------------|
| 美国国家医学图书馆 | 检索生物医学文献和研究文章 | `~~literature` |
| deepsense.ai | 访问bioRxiv和medRxiv预印本 | `~~literature` |
| John Wiley & Sons | 访问学术研究和出版物 | `~~journal access` |
| Sage Bionetworks | 协作研究数据管理 | `~~data repository` |
| deepsense.ai | 生物活性类药物化合物数据库 | `~~chemical database` |
| OpenTargets | 药物靶点发现和优先级排序 | `~~drug targets` |
| deepsense.ai | NIH/NLM临床试验注册库 | `~~clinical trials` |
| BioRender | 科学插图创作 | `~~scientific illustration` |
| Owkin | 生物学AI——组织病理学和药物发现 | `~~AI research` |
| Benchling\* | 实验室数据管理平台 | `~~lab platform` |

### 可选二进制MCP服务器

以下服务器需要单独下载二进制文件：

- **10X Genomics txg-mcp**（`~~genomics platform`）——云分析数据和工作流程（[GitHub](https://github.com/10XGenomics/txg-mcp/releases)）
- **ToolUniverse**（`~~tool database`）——来自哈佛MIMS的科学发现AI工具（[GitHub](https://github.com/mims-harvard/ToolUniverse/releases)）

### 技能（分析工作流程）

#### 单细胞RNA质控
按照scverse最佳实践对scRNA-seq数据进行自动质量控制。支持`.h5ad`和`.h5`文件，采用基于MAD的过滤方法和全面的可视化输出。

#### scvi-tools
用于单细胞组学的深度学习工具包。涵盖scVI、scANVI、totalVI、PeakVI、MultiVI、DestVI、veloVI和sysVI模型，支持整合、批次校正、标签转移和多模态分析。

#### Nextflow流程
在本地或公共GEO/SRA测序数据上运行nf-core生信流程：
- **rnaseq** — 基因表达和差异表达分析
- **sarek** — 胚系和体细胞变异检测（WGS/WES）
- **atacseq** — 染色质可及性分析

#### 仪器数据转Allotrope
将实验室仪器输出文件（PDF、CSV、Excel、TXT）转换为Allotrope简单模型（ASM）格式。支持40+种仪器类型，包括细胞计数器、分光光度计、酶标仪、qPCR和色谱系统。

#### 科学问题选择
基于Fischbach & Walsh框架的研究问题选择系统化框架。包含9个技能，涵盖直觉激发、风险评估、优化函数、决策树、逆境规划和综合分析。

## 快速开始

```bash
# 安装插件
openclaw skills install bio-research

# 运行开始命令查看可用工具
/start
```

## 常见工作流程

**文献综述**
在~~literature数据库中检索文章，通过~~journal access获取全文，并使用~~scientific illustration创建图表。

**单细胞分析**
对scRNA-seq数据进行QC，然后使用scvi-tools进行整合、批次校正和细胞类型注释。

**测序流程**
从GEO/SRA下载公共数据，运行nf-core流程（RNA-seq、变异检测、ATAC-seq），并验证输出结果。

**药物发现**
在~~chemical database中搜索生物活性化合物，使用~~drug target数据库进行靶点优先级排序，并查阅临床试验数据。

**研究策略**
使用科学问题选择框架提出新想法、解决卡顿项目或评估战略决策。

## 许可证

技能基于Apache 2.0许可证授权。MCP服务器由各自的作者提供——具体条款请参阅各服务器文档。
