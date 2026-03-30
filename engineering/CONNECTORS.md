# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为占位符，指代用户在该类别中接入的任何工具。例如，`~~source control` 可能代表 GitHub、GitLab 或任何其他提供 MCP 服务器的版本控制工具。

插件是**工具无关的** — 以类别（代码托管、CI/CD、监控等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但同类别下任何 MCP 服务器均可使用。

在 OpenClaw 中配置 MCP 服务器，请参阅 [MCP_SETUP.md](MCP_SETUP.md)。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他可选项 |
|----------|-------------|-----------------|---------------|
| 沟通 | `~~chat` | Slack | Microsoft Teams |
| 代码托管 | `~~source control` | GitHub | GitLab、Bitbucket |
| 项目追踪 | `~~project tracker` | Linear、Asana、Atlassian（Jira/Confluence） | Shortcut、ClickUp |
| 知识库 | `~~knowledge base` | Notion | Confluence、Guru、Coda |
| 监控 | `~~monitoring` | Datadog | New Relic、Grafana、Splunk |
| 故障管理 | `~~incident management` | PagerDuty | Opsgenie、Incident.io、FireHydrant |
| CI/CD | `~~CI/CD` | — | CircleCI、GitHub Actions、Jenkins、BuildKite |
