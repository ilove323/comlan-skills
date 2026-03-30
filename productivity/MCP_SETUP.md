# MCP 配置指南

## 简介

本插件通过 MCP（模型上下文协议）连接您的通信和项目管理工具，实现自动化任务同步和跨平台记忆构建。以下服务已预配置，可直接连接使用。

## 服务列表

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Slack | https://mcp.slack.com/mcp | 团队消息扫描、行动项发现 |
| Notion | https://mcp.notion.com/mcp | 知识库、参考文档 |
| Asana | https://mcp.asana.com/v2/mcp | 任务同步、项目追踪 |
| Linear | https://mcp.linear.app/mcp | 工单管理、冲刺追踪 |
| Atlassian | https://mcp.atlassian.com/v1/mcp | Jira 任务、Confluence 文档 |
| Microsoft 365 | https://microsoft365.mcp.claude.com/mcp | 邮件、日历、办公文档 |
| monday.com | https://mcp.monday.com/mcp | 项目板、任务管理 |
| ClickUp | https://mcp.clickup.com/mcp | 任务和项目追踪 |
| Google Calendar | https://gcal.mcp.claude.com/mcp | 日程安排、截止日期 |
| Gmail | https://gmail.mcp.claude.com/mcp | 行动项发现 |

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
    "asana": {
      "type": "http",
      "url": "https://mcp.asana.com/v2/mcp"
    },
    "linear": {
      "type": "http",
      "url": "https://mcp.linear.app/mcp"
    },
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "ms365": {
      "type": "http",
      "url": "https://microsoft365.mcp.claude.com/mcp"
    },
    "monday": {
      "type": "http",
      "url": "https://mcp.monday.com/mcp"
    },
    "clickup": {
      "type": "http",
      "url": "https://mcp.clickup.com/mcp"
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
5. 授权完成后，服务将自动包含在后续操作中

如需添加未在上方列出的服务，请在 `.mcp.json` 中添加相应配置，并按提示完成身份验证。
