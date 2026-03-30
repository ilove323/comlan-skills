# 连接器

## 工具引用的工作原理

插件文件使用 `~~category` 作为用户在该类别中连接的任何工具的占位符。例如，`~~chat` 可能代表 Slack、Microsoft Teams 或任何其他具有 MCP 服务器的聊天工具。

插件是**工具无关的**——它们以类别（聊天工具、邮件、云存储等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但该类别中的任何 MCP 服务器均可使用。

本插件在搜索输出中大量使用 `~~category` 引用作为来源标签（如 `~~chat:`、`~~email:`）。这是有意为之——它们代表动态类别标记，解析为已连接的任何工具。

## 本插件的连接器

| 类别 | 占位符 | 内置服务器 | 其他选项 |
|----------|-------------|-----------------|---------------|
| 聊天工具 | `~~chat` | Slack | Microsoft Teams、Discord |
| 电子邮件 | `~~email` | Microsoft 365 | — |
| 云存储 | `~~cloud storage` | Microsoft 365 | Dropbox |
| 知识库 | `~~knowledge base` | Notion、Guru | Confluence、Slite |
| 项目追踪器 | `~~project tracker` | Atlassian (Jira/Confluence)、Asana | Linear、monday.com |
| CRM | `~~CRM` | *（未预配置）* | Salesforce、HubSpot |
| 办公套件 | `~~office suite` | Microsoft 365 | Google Workspace |
