# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为用户在该类别中连接的任意工具的占位符。例如，`~~support platform` 可能代表 Intercom、Zendesk 或任何其他带有 MCP 服务器的支持工具。

插件**与具体工具无关**——它们以类别（支持平台、CRM、即时通讯等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但同类别中的任何 MCP 服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他选项 |
|------|--------|-------------|---------|
| 即时通讯 | `~~chat` | Slack | Microsoft Teams |
| 邮件 | `~~email` | Microsoft 365 | — |
| 云存储 | `~~cloud storage` | Microsoft 365 | — |
| 支持平台 | `~~support platform` | Intercom | Zendesk、Freshdesk、HubSpot Service Hub |
| CRM | `~~CRM` | HubSpot | Salesforce、Pipedrive |
| 知识库 | `~~knowledge base` | Guru、Notion | Confluence、Help Scout |
| 项目跟踪 | `~~project tracker` | Atlassian（Jira/Confluence） | Linear、Asana |
