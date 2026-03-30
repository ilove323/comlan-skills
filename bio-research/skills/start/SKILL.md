---
name: start
description: 设置您的生物研究环境并探索可用工具。当您首次了解插件、检查已连接的文献、药物发现或可视化MCP服务器，或在开始新项目前概览可用分析技能时使用。触发词：开始、生物研究入门、start
---

# 生物研究插件入门

> 如果您看到不熟悉的占位符或需要检查已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

您正在帮助一位生物学研究者熟悉生物研究插件。请按顺序执行以下步骤。

## 第一步：欢迎

显示以下欢迎信息：

```
生物研究插件

您的生命科学AI助手。本插件整合了
文献搜索、数据分析流程和科学研究策略——
一切尽在此处。
```

## 第二步：检查可用的MCP服务器

通过列出可用工具来测试已连接的MCP服务器。将结果分组显示：

**文献与数据来源：**
- ~~literature database — 生物医学文献搜索
- ~~literature database — 预印本获取（生物学和医学）
- ~~journal access — 学术出版物
- ~~data repository — 协作研究数据（Sage Bionetworks）

**药物发现与临床：**
- ~~chemical database — 生物活性化合物数据库
- ~~drug target database — 药物靶点发现平台
- ClinicalTrials.gov — 临床试验注册库
- ~~clinical data platform — 临床试验站点排名与平台帮助

**可视化与AI：**
- ~~scientific illustration — 创建科学图表
- ~~AI research platform — 用于生物学的AI（病理组织学、药物发现）

汇报哪些服务器已连接，哪些尚未配置。

## 第三步：概览可用技能

列出本插件中可用的分析技能：

| 技能 | 功能描述 |
|------|----------|
| **单细胞RNA质控** | 基于MAD过滤的scRNA-seq数据质量控制 |
| **scvi-tools** | 单细胞组学深度学习（scVI、scANVI、totalVI、PeakVI等） |
| **Nextflow流程** | 运行nf-core流程（RNA-seq、WGS/WES、ATAC-seq） |
| **仪器数据转换** | 将实验室仪器输出转换为Allotrope ASM格式 |
| **科学问题选择** | 系统性研究问题选择框架 |

## 第四步：可选配置——二进制MCP服务器

提示用户有两个额外的MCP服务器可单独安装：

- **~~genomics platform** — 访问云端分析数据和工作流
  安装方式：从 https://github.com/10XGenomics/txg-mcp/releases 下载 `txg-node.mcpb`
- **~~tool database**（哈佛MIMS）— 用于科学发现的AI工具
  安装方式：从 https://github.com/mims-harvard/ToolUniverse/releases 下载 `tooluniverse.mcpb`

这些需要下载二进制文件，为可选安装项。

## 第五步：询问如何提供帮助

询问研究者今天的工作内容，并根据常见工作流提供建议：

1. **文献综述** — "在~~literature database中搜索[主题]的最新论文"
2. **分析测序数据** — "对我的单细胞数据进行质控"或"配置RNA-seq流程"
3. **药物发现** — "在~~chemical database中搜索靶向[蛋白]的化合物"或"查找[疾病]的药物靶点"
4. **数据标准化** — "将我的仪器数据转换为Allotrope格式"
5. **研究策略** — "帮我评估一个新的项目想法"

等待用户回应，并引导他们使用合适的工具和技能。
