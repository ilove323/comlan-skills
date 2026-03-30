# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为占位符，代表用户在该类别中连接的任何工具。例如，`~~data warehouse` 可能指Snowflake、BigQuery或任何其他具有MCP服务器的数据仓库。

插件**与具体工具无关**——以类别（数据仓库、协作工具、项目管理工具等）而非特定产品来描述工作流程。`.mcp.json` 预配置了特定MCP服务器，但该类别中的任何MCP服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他可选 |
|------|-------|------------|---------|
| 数据仓库 | `~~data warehouse` | Snowflake\*、Databricks\*、BigQuery | Redshift、PostgreSQL |
| 邮件 | `~~email` | Microsoft 365 | — |
| Office套件 | `~~office suite` | Microsoft 365 | — |
| 团队协作 | `~~chat` | Slack | Microsoft Teams |
| ERP / 会计系统 | `~~erp` | —（暂无支持的MCP服务器） | NetSuite、SAP、QuickBooks、Xero |
| 分析/商业智能 | `~~analytics` | —（暂无支持的MCP服务器） | Tableau、Looker、Power BI |

\* 占位符——MCP URL尚未配置
