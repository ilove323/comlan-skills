# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为占位符，代表用户在该类别中连接的任何工具。例如，`~~marketing automation` 可能指HubSpot、Marketo或任何其他具有MCP服务器的营销平台。

插件**与具体工具无关**——以类别（设计、SEO、邮件营销等）而非特定产品来描述工作流程。`.mcp.json` 预配置了特定MCP服务器，但该类别中的任何MCP服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他可选 |
|------|-------|------------|---------|
| 团队协作 | `~~chat` | Slack | Microsoft Teams |
| 设计 | `~~design` | Canva、Figma | Adobe Creative Cloud |
| 营销自动化 | `~~marketing automation` | HubSpot | Marketo、Pardot、Mailchimp |
| 产品分析 | `~~product analytics` | Amplitude | Mixpanel、Google Analytics |
| 知识库 | `~~knowledge base` | Notion | Confluence、Guru |
| SEO工具 | `~~SEO` | Ahrefs、Similarweb | Semrush、Moz |
| 邮件营销 | `~~email marketing` | Klaviyo | Mailchimp、Brevo、Customer.io |
| 营销分析 | `~~marketing analytics` | Supermetrics | Google Analytics、Mailchimp、Semrush |
