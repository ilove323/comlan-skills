# MCP 服务器配置

本插件通过 MCP（Model Context Protocol）服务器连接您的客户支持工具，提升工单处理、客户调研和回复起草的效率。以下是各服务的配置说明。

## 支持的服务

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Slack | https://mcp.slack.com/mcp | 内部讨论、客户频道背景 |
| Intercom | https://mcp.intercom.com/mcp | 支持平台、工单历史、客户对话 |
| HubSpot | https://mcp.hubspot.com/anthropic | CRM 账户详情、联系人信息 |
| Guru | https://mcp.api.getguru.com/mcp | 知识库、内部文档 |
| Atlassian | https://mcp.atlassian.com/v1/mcp | 项目跟踪（Jira/Confluence） |
| Notion | https://mcp.notion.com/mcp | 知识库、内部文档 |
| Microsoft 365 | https://microsoft365.mcp.claude.com/mcp | 邮件、云存储 |
| Google Calendar | https://gcal.mcp.claude.com/mcp | 日历 |
| Gmail | https://gmail.mcp.claude.com/mcp | 邮件 |

## 配置方法

将以下内容添加到 `~/.openclaw/openclaw.json` 的 `mcpServers` 字段中：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "intercom": {
      "type": "http",
      "url": "https://mcp.intercom.com/mcp"
    },
    "hubspot": {
      "type": "http",
      "url": "https://mcp.hubspot.com/anthropic"
    },
    "guru": {
      "type": "http",
      "url": "https://mcp.api.getguru.com/mcp"
    },
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
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

## 授权说明

大多数 MCP 服务需要 OAuth 授权。首次连接时，OpenClaw 会引导您完成授权流程。请确保您已登录对应服务的账户，并拥有必要的权限。
