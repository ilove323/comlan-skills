# 连接器

## 工具引用的工作原理

插件文件使用 `~~category` 作为用户在该类别中连接的任何工具的占位符。例如，`~~ITSM` 可能代表 ServiceNow、Zendesk 或任何其他具有 MCP 服务器的服务管理工具。

插件是**工具无关的**——它们以类别（ITSM、项目追踪器、知识库等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但该类别中的任何 MCP 服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 内置服务器 | 其他选项 |
|----------|-------------|-----------------|---------------|
| 日历 | `~~calendar` | Google Calendar | Microsoft 365 |
| 聊天工具 | `~~chat` | Slack | Microsoft Teams |
| 电子邮件 | `~~email` | Gmail、Microsoft 365 | — |
| ITSM | `~~ITSM` | ServiceNow | Zendesk、Freshservice、Jira Service Management |
| 知识库 | `~~knowledge base` | Notion、Atlassian (Confluence) | Guru、Coda |
| 项目追踪器 | `~~project tracker` | Asana、Atlassian (Jira) | Linear、monday.com、ClickUp |
| 采购系统 | `~~procurement` | — | Coupa、SAP Ariba、Zip |
| 办公套件 | `~~office suite` | Microsoft 365 | Google Workspace |
