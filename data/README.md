# 数据分析插件

专为 [OpenClaw](https://openclaw.ai) 设计的数据分析插件，同时支持 Claude Code。覆盖 SQL 查询、数据探索、可视化、仪表板构建和洞察生成。兼容任何数据仓库、任何 SQL 方言和任何分析技术栈。

## 安装

将插件目录复制到 ~/.openclaw/skills/data/
或使用 ClawHub: openclaw skills install data

## 功能概览

本插件将 AI助手 转化为数据分析协作伙伴，帮助你探索数据集、编写优化 SQL、构建可视化图表、创建交互式仪表板，并在分享给干系人之前验证分析结果。

### 已接入数据仓库时

接入数据仓库 MCP 服务器（如 Snowflake、Databricks、BigQuery 或任何 SQL 兼容数据库）可获得最佳体验。AI助手将：

- 直接查询你的数据仓库
- 探索 schema 和表元数据
- 端到端执行分析，无需手动复制粘贴
- 根据查询结果迭代优化

### 未接入数据仓库时

未接入数据仓库时，可粘贴 SQL 查询结果或上传 CSV/Excel 文件进行分析和可视化。AI助手也可以帮你编写 SQL 查询，供你手动执行后再分析返回结果。

## 命令

| 命令 | 说明 |
|---------|-------------|
| `/analyze` | 回答数据问题——从快速查询到完整分析 |
| `/explore-data` | 对数据集进行剖析和探索，了解其形态、质量和规律 |
| `/write-query` | 按你使用的方言编写遵循最佳实践的优化 SQL |
| `/create-viz` | 用 Python 创建发布级数据可视化图表 |
| `/build-dashboard` | 构建带筛选器和图表的交互式 HTML 仪表板 |
| `/validate` | 分享前对分析进行 QA——方法论、准确性和偏差检查 |

## 技能

| 技能 | 说明 |
|-------|-------------|
| `sql-queries` | 跨方言 SQL 最佳实践、常见模式和性能优化 |
| `data-exploration` | 数据剖析、质量评估和规律发现 |
| `data-visualization` | 图表选择、Python 可视化代码模式和设计原则 |
| `statistical-analysis` | 描述性统计、趋势分析、异常值检测和假设检验 |
| `data-validation` | 交付前 QA、合理性检查和文档规范 |
| `interactive-dashboard-builder` | 使用 Chart.js 构建 HTML/JS 仪表板，支持筛选器和样式定制 |

## 工作流示例

### 即席分析

```
你：/analyze 过去 12 个月的月度营收趋势，按产品线拆分是多少？

AI助手：[编写 SQL 查询] → [对数据仓库执行] → [生成趋势图]
       → [识别关键规律："产品线 A 同比增长 23%，B 持平"]
       → [通过合理性检查验证结果]
```

### 数据探索

```
你：/explore-data users 表

AI助手：[剖析表结构：230 万行，47 列]
       → [报告：created_at 有 0.2% 空值，email 唯一性率 99.8%]
       → [标记：status 列在 340 行中出现异常值 "UNKNOWN"]
       → [建议："高价值分析维度：plan_type、signup_source、country"]
```

### 编写查询

```
你：/write-query 我需要一个队列留存分析——按注册月份对用户分组，
     显示 1、3、6、12 个月后的活跃用户比例。我们使用 Snowflake。

AI助手：[编写带 CTE 的优化 Snowflake SQL]
       → [添加注释说明每个步骤]
       → [包含分区裁剪的性能说明]
```

### 构建仪表板

```
你：/build-dashboard 创建一个销售仪表板，包含月度营收、头部产品和区域分布。数据如下：[粘贴 CSV]

AI助手：[生成独立 HTML 文件]
       → [包含交互式 Chart.js 可视化]
       → [添加区域和时间段的下拉筛选器]
       → [在浏览器中打开预览]
```

### 分享前验证

```
你：/validate [分享分析文档]

AI助手：[审查方法论] → [检查流失分析中的幸存者偏差]
       → [验证聚合逻辑] → [标记："分母排除了试用用户，
          可能高估转化率约 5 个百分点"]
       → [置信度评估："在注明的前提条件下可以分享"]
```

## 接入数据栈

> 如遇到不熟悉的占位符或需要查看哪些工具已接入，请参阅 [CONNECTORS.md](CONNECTORS.md)。MCP 服务器的详细配置说明见 [MCP_SETUP.md](MCP_SETUP.md)。

接入数据基础设施可获得最佳体验。可为以下工具添加 MCP 服务器：

- **数据仓库**：Snowflake、Databricks、BigQuery、Definite 或任何 SQL 兼容数据库
- **分析/BI**：Amplitude、Looker、Tableau 等
- **Notebook**：Jupyter、Hex 等
- **表格工具**：Google Sheets、Excel
- **数据编排**：Airflow、dbt、Dagster、Prefect
- **数据接入**：Fivetran、Airbyte、Stitch

在 `.mcp.json` 或 OpenClaw 设置中配置 MCP 服务器以启用直接数据访问。
