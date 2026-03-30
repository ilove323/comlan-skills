# MCP 服务器配置

本插件通过 MCP 服务器接入项目管理、沟通协作、知识库、设计和产品分析等工具，为产品管理工作流提供数据支撑。

## 支持的服务

| 服务名称（中文） | 服务标识 | URL | 用途 |
|---|---|---|---|
| Slack（团队沟通） | slack | https://mcp.slack.com/mcp | 获取团队对话和干系人讨论线索 |
| Linear（项目追踪） | linear | https://mcp.linear.app/mcp | 路线图集成、工单上下文和状态追踪 |
| Asana（项目追踪） | asana | https://mcp.asana.com/v2/mcp | 路线图集成、工单上下文和状态追踪 |
| Monday.com（项目追踪） | monday | https://mcp.monday.com/mcp | 路线图集成和状态追踪 |
| ClickUp（项目追踪） | clickup | https://mcp.clickup.com/mcp | 路线图集成和状态追踪 |
| Atlassian / Jira（项目追踪） | atlassian | https://mcp.atlassian.com/v1/mcp | 路线图集成、工单上下文和状态追踪 |
| Notion（知识库） | notion | https://mcp.notion.com/mcp | 读取现有规格文档、研究报告和会议记录 |
| Figma（设计） | figma | https://mcp.figma.com/mcp | 设计上下文和交付物参考 |
| Amplitude（产品分析） | amplitude | https://mcp.amplitude.com/mcp | 使用数据、指标和行为分析 |
| Amplitude EU（产品分析） | amplitude-eu | https://mcp.eu.amplitude.com/mcp | 欧洲区 Amplitude 数据 |
| Pendo（产品分析） | pendo | https://app.pendo.io/mcp/v0/shttp | 用户引导和功能采用率数据 |
| Intercom（用户反馈） | intercom | https://mcp.intercom.com/mcp | 支持工单、功能需求和用户对话 |
| Fireflies（会议记录） | fireflies | https://api.fireflies.ai/mcp | 会议纪要和讨论内容 |
| Google Calendar（日历） | google-calendar | https://gcal.mcp.claude.com/mcp | 日历上下文和会议信息 |
| Gmail（邮件） | gmail | https://gmail.mcp.claude.com/mcp | 邮件沟通上下文 |
| Similarweb（竞品情报） | similarweb | https://mcp.similarweb.com/mcp | 竞争对手流量和市场数据 |

## 在 OpenClaw 中配置

将以下内容添加到 `~/.openclaw/openclaw.json` 的 `mcpServers` 字段（按需选择所需服务）：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "linear": {
      "type": "http",
      "url": "https://mcp.linear.app/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "figma": {
      "type": "http",
      "url": "https://mcp.figma.com/mcp"
    },
    "amplitude": {
      "type": "http",
      "url": "https://mcp.amplitude.com/mcp"
    },
    "intercom": {
      "type": "http",
      "url": "https://mcp.intercom.com/mcp"
    },
    "fireflies": {
      "type": "http",
      "url": "https://api.fireflies.ai/mcp"
    },
    "similarweb": {
      "type": "http",
      "url": "https://mcp.similarweb.com/mcp"
    }
  }
}
```

如需 OAuth 授权，请在 OpenClaw 中启用服务后按提示完成授权。
