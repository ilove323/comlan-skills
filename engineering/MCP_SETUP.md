# MCP 服务器配置

本插件通过 MCP 服务器接入代码托管、项目追踪、监控告警、故障管理和协作工具，为工程团队的日常工作流提供完整的上下文支撑。

## 支持的服务

| 服务名称（中文） | 服务标识 | URL | 用途 |
|---|---|---|---|
| Slack（团队沟通） | slack | https://mcp.slack.com/mcp | 团队讨论、站会频道 |
| Linear（项目追踪） | linear | https://mcp.linear.app/mcp | 工单状态、迭代数据、任务分配 |
| Asana（项目追踪） | asana | https://mcp.asana.com/v2/mcp | 工单状态、迭代数据、任务分配 |
| Atlassian / Jira（项目追踪） | atlassian | https://mcp.atlassian.com/v1/mcp | 工单状态、迭代数据、任务分配 |
| Notion（知识库） | notion | https://mcp.notion.com/mcp | ADR 文档、Runbook、入职指南 |
| GitHub（代码托管） | github | https://api.github.com/mcp | PR 差异、提交历史、分支状态 |
| PagerDuty（故障管理） | pagerduty | https://mcp.pagerduty.com/mcp | 值班安排、故障追踪、告警通知 |
| Datadog（监控） | datadog | https://mcp.datadoghq.com/mcp | 日志、指标、告警和仪表板 |
| Google Calendar（日历） | google-calendar | https://gcal.mcp.claude.com/mcp | 日历和会议信息 |
| Gmail（邮件） | gmail | https://gmail.mcp.claude.com/mcp | 邮件沟通上下文 |

## 在 OpenClaw 中配置

将以下内容添加到 `~/.openclaw/openclaw.json` 的 `mcpServers` 字段（按需选择所需服务）：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp"
    },
    "linear": {
      "type": "http",
      "url": "https://mcp.linear.app/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "datadog": {
      "type": "http",
      "url": "https://mcp.datadoghq.com/mcp"
    },
    "pagerduty": {
      "type": "http",
      "url": "https://mcp.pagerduty.com/mcp"
    }
  }
}
```

如需 OAuth 授权，请在 OpenClaw 中启用服务后按提示完成授权。
