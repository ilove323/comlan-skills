# MCP 服务器配置

本插件通过 MCP（Model Context Protocol）服务器连接您的法务工具，支持合同审查、NDA 分类、合规检查和文件签署等工作流。以下是各服务的配置说明。

## 支持的服务

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Slack | https://mcp.slack.com/mcp | 团队请求、通知、分类 |
| Box | https://mcp.box.com | 规范手册、模板、先例文件 |
| Egnyte | https://mcp-server.egnyte.com/mcp | 云存储、文件管理 |
| Atlassian | https://mcp.atlassian.com/v1/mcp | 事项跟踪、任务管理（Jira/Confluence） |
| Microsoft 365 | https://microsoft365.mcp.claude.com/mcp | 邮件、日历、文档 |
| DocuSign | https://mcp.docusign.com/mcp | 电子签署 |
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
    "box": {
      "type": "http",
      "url": "https://mcp.box.com"
    },
    "egnyte": {
      "type": "http",
      "url": "https://mcp-server.egnyte.com/mcp"
    },
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "ms365": {
      "type": "http",
      "url": "https://microsoft365.mcp.claude.com/mcp"
    },
    "docusign": {
      "type": "http",
      "url": "https://mcp.docusign.com/mcp"
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

大多数 MCP 服务需要 OAuth 授权。首次连接时，OpenClaw 会引导您完成授权流程。请确保您已登录对应服务的账户，并拥有访问法务文件和工具所需的权限。
