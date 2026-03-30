# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为占位符，指代用户在该类别中接入的任何工具。例如，`~~data warehouse` 可能代表 Snowflake、BigQuery 或任何其他提供 MCP 服务器的数据仓库。

插件是**工具无关的** — 以类别（数据仓库、Notebook、产品分析等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但同类别下任何 MCP 服务器均可使用。

在 OpenClaw 中配置 MCP 服务器，请参阅 [MCP_SETUP.md](MCP_SETUP.md)。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他可选项 |
|----------|-------------|-----------------|---------------|
| 数据仓库 | `~~data warehouse` | Snowflake\*、Databricks\*、BigQuery、Definite | Redshift、PostgreSQL、MySQL |
| Notebook | `~~notebook` | Hex | Jupyter、Deepnote、Observable |
| 产品分析 | `~~product analytics` | Amplitude | Mixpanel、Heap |
| 项目追踪 | `~~project tracker` | Atlassian（Jira/Confluence） | Linear、Asana |

\* 占位符——MCP URL 尚未配置
