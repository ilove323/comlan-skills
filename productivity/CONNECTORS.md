# 连接器

## 工具引用的工作原理

插件文件使用 `~~category` 作为用户在该类别中连接的任何工具的占位符。例如，`~~project tracker` 可能代表 Asana、Linear、Jira 或任何其他具有 MCP 服务器的项目追踪工具。

插件是**工具无关的**——它们以类别（聊天工具、项目追踪器、知识库等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但该类别中的任何 MCP 服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 内置服务器 | 其他选项 |
|----------|-------------|-----------------|---------------|
| 聊天工具 | `~~chat` | Slack | Microsoft Teams、Discord |
| 电子邮件 | `~~email` | Microsoft 365 | — |
| 日历 | `~~calendar` | Microsoft 365 | — |
| 知识库 | `~~knowledge base` | Notion | Confluence、Guru、Coda |
| 项目追踪器 | `~~project tracker` | Asana、Linear、Atlassian (Jira/Confluence)、monday.com、ClickUp | Shortcut、Basecamp、Wrike |
| 办公套件 | `~~office suite` | Microsoft 365 | — |
