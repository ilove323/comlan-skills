# MCP 配置指南

## 简介

本插件通过 MCP（模型上下文协议）连接企业各平台，实现跨工具统一搜索。以下服务已预配置，可直接连接使用。连接的来源越多，搜索结果越完整。

## 服务列表

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Slack | https://mcp.slack.com/mcp | 聊天消息、频道、讨论串搜索 |
| Notion | https://mcp.notion.com/mcp | 知识库文档搜索 |
| Guru | https://mcp.api.getguru.com/mcp | 知识卡片、内部文档搜索 |
| Atlassian | https://mcp.atlassian.com/v1/mcp | Jira 工单、Confluence 文档搜索 |
| Asana | https://mcp.asana.com/v2/mcp | 任务、项目搜索 |
| Microsoft 365 | https://microsoft365.mcp.claude.com/mcp | 邮件、文档、云存储搜索 |
| Google Calendar | https://gcal.mcp.claude.com/mcp | 日程活动搜索 |
| Gmail | https://gmail.mcp.claude.com/mcp | 邮件搜索 |

## OpenClaw 配置

在您的 OpenClaw 配置中添加以下内容以启用 MCP 连接：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "guru": {
      "type": "http",
      "url": "https://mcp.api.getguru.com/mcp"
    },
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "asana": {
      "type": "http",
      "url": "https://mcp.asana.com/v2/mcp"
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

## OAuth 授权说明

上述大多数服务使用 OAuth 2.0 进行身份验证。首次连接时，系统将引导您完成授权流程：

1. 在 OpenClaw 的 MCP 设置中添加服务配置
2. 系统提示时点击"授权"按钮
3. 在弹出的浏览器窗口中登录您的账户
4. 授权 OpenClaw 访问所需权限
5. 授权完成后，服务将自动包含在后续搜索中

如需添加 CRM（如 Salesforce、HubSpot）或其他未在上方列出的服务，请在 `.mcp.json` 中添加相应配置，并按提示完成身份验证。添加后，新来源将自动包含在后续搜索中。
