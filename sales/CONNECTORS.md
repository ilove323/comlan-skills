# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为用户在该类别中连接的任意工具的占位符。例如，`~~CRM` 可能代表 Salesforce、HubSpot 或任何其他带有 MCP 服务器的 CRM。

插件**与具体工具无关**——它们以类别（CRM、即时通讯、邮件等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但同类别中的任何 MCP 服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他选项 |
|------|--------|-------------|---------|
| 日历 | `~~calendar` | Google Calendar、Microsoft 365 | — |
| 即时通讯 | `~~chat` | Slack | Microsoft Teams |
| 竞争情报 | `~~competitive intelligence` | Similarweb | Crayon、Klue |
| CRM | `~~CRM` | HubSpot、Close | Salesforce、Pipedrive、Copper |
| 数据富化 | `~~data enrichment` | Clay、ZoomInfo、Apollo | Clearbit、Lusha |
| 邮件 | `~~email` | Gmail、Microsoft 365 | — |
| 知识库 | `~~knowledge base` | Notion | Confluence、Guru |
| 会议转录 | `~~conversation intelligence` | Fireflies | Gong、Chorus、Otter.ai |
| 项目跟踪 | `~~project tracker` | Atlassian（Jira/Confluence） | Linear、Asana |
| 销售互动 | `~~sales engagement` | Outreach | Salesloft、Apollo |
