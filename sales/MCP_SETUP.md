# MCP 服务器配置

本插件通过 MCP（Model Context Protocol）服务器连接您的销售工具，显著提升技能效果。以下是各服务的配置说明。

## 支持的服务

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Slack | https://mcp.slack.com/mcp | 内部讨论、同事情报 |
| HubSpot | https://mcp.hubspot.com/anthropic | CRM 管道数据、客户历史 |
| Close | https://mcp.close.com/mcp | CRM 管道数据、联系人记录 |
| Clay | https://api.clay.com/v3/mcp | 数据富化、公司与联系人信息 |
| ZoomInfo | https://mcp.zoominfo.com/mcp | 数据富化、企业与联系人数据 |
| Notion | https://mcp.notion.com/mcp | 知识库、内部文档 |
| Atlassian | https://mcp.atlassian.com/v1/mcp | 项目跟踪（Jira/Confluence） |
| Fireflies | https://api.fireflies.ai/mcp | 通话录音与转录 |
| Microsoft 365 | https://microsoft365.mcp.claude.com/mcp | 邮件、日历、文档 |
| Apollo | https://api.apollo.io/mcp | 数据富化、潜在客户信息 |
| Outreach | https://mcp.outreach.io/mcp | 销售互动序列 |
| Google Calendar | https://gcal.mcp.claude.com/mcp | 日历与会议 |
| Gmail | https://gmail.mcp.claude.com/mcp | 邮件 |
| SimilarWeb | https://mcp.similarweb.com/mcp | 竞争情报 |

## 配置方法

将以下内容添加到 `~/.openclaw/openclaw.json` 的 `mcpServers` 字段中：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "hubspot": {
      "type": "http",
      "url": "https://mcp.hubspot.com/anthropic"
    },
    "close": {
      "type": "http",
      "url": "https://mcp.close.com/mcp"
    },
    "clay": {
      "type": "http",
      "url": "https://api.clay.com/v3/mcp"
    },
    "zoominfo": {
      "type": "http",
      "url": "https://mcp.zoominfo.com/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "fireflies": {
      "type": "http",
      "url": "https://api.fireflies.ai/mcp"
    },
    "ms365": {
      "type": "http",
      "url": "https://microsoft365.mcp.claude.com/mcp"
    },
    "apollo": {
      "type": "http",
      "url": "https://api.apollo.io/mcp"
    },
    "outreach": {
      "type": "http",
      "url": "https://mcp.outreach.io/mcp"
    },
    "google-calendar": {
      "type": "http",
      "url": "https://gcal.mcp.claude.com/mcp"
    },
    "gmail": {
      "type": "http",
      "url": "https://gmail.mcp.claude.com/mcp"
    },
    "similarweb": {
      "type": "http",
      "url": "https://mcp.similarweb.com/mcp"
    }
  }
}
```

## 授权说明

大多数 MCP 服务需要 OAuth 授权。首次连接时，OpenClaw 会引导您完成授权流程。请确保您已登录对应服务的账户，并拥有必要的权限。
