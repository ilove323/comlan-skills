# MCP 服务器配置

本插件通过 MCP 服务器接入数据仓库、Notebook、产品分析和项目追踪工具，支持端到端的数据查询、分析和可视化工作流。

## 支持的服务

| 服务名称（中文） | 服务标识 | URL | 用途 |
|---|---|---|---|
| Snowflake（数据仓库） | snowflake | 需手动配置 | 直接查询 Snowflake 数据仓库 |
| Databricks（数据仓库） | databricks | 需手动配置 | 直接查询 Databricks 数据湖仓 |
| BigQuery（数据仓库） | bigquery | https://bigquery.googleapis.com/mcp | 直接查询 Google BigQuery |
| Hex（Notebook） | hex | https://app.hex.tech/mcp | 运行 Notebook、共享分析结果 |
| Amplitude（产品分析） | amplitude | https://mcp.amplitude.com/mcp | 获取使用数据、行为指标 |
| Amplitude EU（产品分析） | amplitude-eu | https://mcp.eu.amplitude.com/mcp | 欧洲区 Amplitude 数据 |
| Atlassian / Jira（项目追踪） | atlassian | https://mcp.atlassian.com/v1/mcp | 工单上下文和任务追踪 |
| Definite（数据分析平台） | definite | https://api.definite.app/v3/mcp/http | 数据探索和 BI 查询 |

## 在 OpenClaw 中配置

将以下内容添加到 `~/.openclaw/openclaw.json` 的 `mcpServers` 字段（按需选择所需服务）：

```json
{
  "mcpServers": {
    "bigquery": {
      "type": "http",
      "url": "https://bigquery.googleapis.com/mcp"
    },
    "amplitude": {
      "type": "http",
      "url": "https://mcp.amplitude.com/mcp"
    },
    "hex": {
      "type": "http",
      "url": "https://app.hex.tech/mcp"
    },
    "definite": {
      "type": "http",
      "url": "https://api.definite.app/v3/mcp/http"
    }
  }
}
```

> **注意**：Snowflake 和 Databricks 的 MCP URL 尚未预配置，需在上方填入实际的服务端点地址。

如需 OAuth 授权，请在 OpenClaw 中启用服务后按提示完成授权。
