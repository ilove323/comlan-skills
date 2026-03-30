# MCP服务配置指南

本插件通过MCP服务连接您的财务数据源，实现自动化数据拉取和分析。

## 已配置的MCP服务

| 服务名称 | URL | 用途 |
|---------|-----|------|
| snowflake | （待配置） | 数据仓库——存储和查询财务数据、运行差异分析 |
| databricks | （待配置） | 数据仓库——大规模财务数据分析 |
| bigquery | https://bigquery.googleapis.com/mcp | 数据仓库——Google BigQuery财务数据查询 |
| slack | https://mcp.slack.com/mcp | 团队协作——分享报告和结账状态更新 |
| ms365 | https://microsoft365.mcp.claude.com/mcp | Office套件——工作底稿生成、Excel数据处理 |
| google-calendar | https://gcal.mcp.claude.com/mcp | 日历——安排结账会议和截止日期提醒 |
| gmail | https://gmail.mcp.claude.com/mcp | 邮件——发送报告和请求审批 |

## OpenClaw配置示例

将以下配置添加至您的 `.mcp.json` 文件（已包含在本插件目录的 `.mcp.json` 中）：

```json
{
  "mcpServers": {
    "snowflake": {
      "type": "http",
      "url": ""
    },
    "databricks": {
      "type": "http",
      "url": ""
    },
    "bigquery": {
      "type": "http",
      "url": "https://bigquery.googleapis.com/mcp"
    },
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "ms365": {
      "type": "http",
      "url": "https://microsoft365.mcp.claude.com/mcp"
    },
    "google-calendar": {
      "type": "http",
      "url": "https://gcal.mcp.claude.com/mcp"
    },
    "gmail": {
      "type": "http",
      "url": "https://gmail.mcp.claude.com/mcp"
    }
  }
}
```

## OAuth认证说明

部分服务需要OAuth认证。首次连接时，OpenClaw会引导您完成授权流程：

- **Microsoft 365**：使用您的企业Microsoft账户授权
- **Google服务**（BigQuery、Google Calendar、Gmail）：使用您的Google账户授权
- **Slack**：在Slack中授权OpenClaw应用

Snowflake和Databricks的URL需由您的IT团队或数据工程团队提供，配置完成后填入对应的 `url` 字段。

## 无集成时的使用方式

即使不连接任何MCP服务，本插件的所有功能仍可正常使用——您可以通过粘贴数据、上传文件或手动提供信息来完成分析。连接MCP服务后，AI助手可以自动拉取数据，大幅提升工作效率。
