# 财务与会计插件

专为OpenClaw设计的财务会计插件——支持月末结账、记账凭证编制、账户对账、财务报表生成、差异分析和SOX审计支持。

> **重要提示**：本插件协助完成财务和会计工作流程，不提供财务、税务或审计建议。所有输出在用于财务报告、监管申报或审计文档前，均应由具备资质的财务专业人员审核。

## 安装

```bash
openclaw plugins add knowledge-work-plugins/finance
```

## 命令

| 命令 | 描述 |
|------|------|
| `/journal-entry` | 记账凭证编制——生成应计、固定资产、预付费用、薪资和收入凭证，含正确的借贷方向和支持明细 |
| `/reconciliation` | 账户对账——比对总账余额与子账、银行或第三方余额，识别未对账项 |
| `/income-statement` | 利润表生成——生成含期间对比和差异分析的利润表 |
| `/variance-analysis` | 差异/波动分析——将差异分解为驱动因素，含叙述性说明和瀑布图分析 |
| `/sox-testing` | SOX合规测试——生成样本选取、测试工作底稿和控制评估 |

## 技能

| 技能 | 描述 |
|------|------|
| `journal-entry-prep` | 凭证编制最佳实践、标准应计类型、支持文件要求和审核工作流程 |
| `reconciliation` | 总账对子账、银行对账和内部往来对账方法论，含未对账项分类和账龄分析 |
| `financial-statements` | 利润表、资产负债表和现金流量表格式，含GAAP列报要求和波动分析方法论 |
| `variance-analysis` | 差异分解技术（量/价、费率/结构）、重要性阈值、叙述性说明生成和瀑布图 |
| `close-management` | 月末结账清单、任务排序、依赖关系、状态跟踪和按天划分的常见结账活动 |
| `audit-support` | SOX 404控制测试方法论、样本选取、文档标准和缺陷分类 |

## 典型工作流程示例

### 月末结账

1. 运行 `/journal-entry ap-accrual 2024-12` 生成应付账款应计凭证
2. 运行 `/journal-entry prepaid 2024-12` 摊销预付费用
3. 运行 `/journal-entry fixed-assets 2024-12` 计提折旧
4. 运行 `/reconciliation cash 2024-12` 进行银行对账
5. 运行 `/reconciliation accounts-receivable 2024-12` 对应收账款子账
6. 运行 `/income-statement monthly 2024-12` 生成含波动分析的利润表

### 差异分析

1. 运行 `/variance-analysis revenue 2024-Q4 vs 2024-Q3` 分析收入差异
2. 运行 `/variance-analysis opex 2024-12 vs budget` 调查运营费用差异
3. 审查瀑布图分析，并对无法解释的差异提供背景说明

### SOX测试

1. 运行 `/sox-testing revenue-recognition 2024-Q4` 生成收入控制测试工作底稿
2. 运行 `/sox-testing procure-to-pay 2024-Q4` 测试采购控制
3. 审查样本选取并记录测试结果

## MCP集成

> 如遇不熟悉的占位符或需要查看已连接工具，请参见 [CONNECTORS.md](CONNECTORS.md)。如需配置MCP服务，请参见 [MCP_SETUP.md](MCP_SETUP.md)。

本插件在连接财务数据源的MCP服务后效果最佳。在插件目录的 `.mcp.json` 中添加相关服务：

### ERP / 会计系统

连接您的ERP（如NetSuite、SAP）MCP服务，自动拉取试算平衡表、子账数据和记账凭证。

### 数据仓库

连接数据仓库（如Snowflake、BigQuery）MCP服务，查询财务数据、运行差异分析和拉取历史对比数据。

### 电子表格

连接电子表格工具（如Google Sheets、Excel）用于生成工作底稿、对账模板和财务模型更新。

### 分析/商业智能

连接BI平台（如Tableau、Looker）拉取仪表板、KPI和趋势数据，用于差异分析说明。

> **说明：** 连接ERP和数据仓库MCP服务可自动拉取财务数据。如未连接，可粘贴数据或上传文件进行分析。

## 配置

在本插件目录 `.mcp.json` 的 `mcpServers` 部分添加您的数据源MCP服务。`recommendedCategories` 字段列出了能增强本插件功能的集成类型：

- `erp-accounting` — 用于总账、子账和凭证数据的ERP或会计系统
- `data-warehouse` — 用于财务查询和历史数据的数据仓库
- `spreadsheets` — 用于工作底稿生成的电子表格工具
- `analytics-bi` — 用于仪表板和KPI数据的BI工具
- `documents` — 用于政策文件、备忘录和支持材料的文档存储
- `email` — 用于发送报告和请求审批的邮件
- `chat` — 用于结账状态更新和问题沟通的团队协作工具
