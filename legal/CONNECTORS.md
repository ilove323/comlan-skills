# 连接器

## 工具引用的工作原理

插件文件使用 `~~类别` 作为占位符，代表用户在该类别中连接的任意工具。例如，`~~cloud storage` 可能指 Box、Egnyte 或任何其他具有 MCP 服务器的存储提供商。

插件是**工具无关的**——它们以类别（云存储、即时通讯、办公套件等）而非具体产品来描述工作流。`~/.openclaw/openclaw.json` 预配置了特定的 MCP 服务器，但该类别中的任何 MCP 服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 已包含的服务器 | 其他选项 |
|------|--------|--------------|---------|
| 日历 | `~~calendar` | Google Calendar | Microsoft 365 |
| 即时通讯 | `~~chat` | Slack | Microsoft Teams |
| 云存储 | `~~cloud storage` | Box、Egnyte | Dropbox、SharePoint、Google Drive |
| CLM | `~~CLM` | — | Ironclad、Agiloft |
| CRM | `~~CRM` | — | Salesforce、HubSpot |
| 邮件 | `~~email` | Gmail | Microsoft 365 |
| 电子签署 | `~~e-signature` | DocuSign | Adobe Sign |
| 办公套件 | `~~office suite` | Microsoft 365 | Google Workspace |
| 项目跟踪器 | `~~project tracker` | Atlassian（Jira/Confluence） | Linear、Asana |
