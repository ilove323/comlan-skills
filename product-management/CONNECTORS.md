# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为占位符，指代用户在该类别中接入的任何工具。例如，`~~project tracker` 可能代表 Linear、Asana、Jira 或任何其他提供 MCP 服务器的项目追踪工具。

插件是**工具无关的** — 以类别（项目追踪、设计、产品分析等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但同类别下任何 MCP 服务器均可使用。

在 OpenClaw 中配置 MCP 服务器，请参阅 [MCP_SETUP.md](MCP_SETUP.md)。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他可选项 |
|----------|-------------|-----------------|---------------|
| 日历 | `~~calendar` | Google Calendar | Microsoft 365 |
| 沟通 | `~~chat` | Slack | Microsoft Teams |
| 竞品情报 | `~~competitive intelligence` | Similarweb | Crayon、Klue |
| 设计 | `~~design` | Figma | Sketch、Adobe XD |
| 邮件 | `~~email` | Gmail | Microsoft 365 |
| 知识库 | `~~knowledge base` | Notion | Confluence、Guru、Coda |
| 会议记录 | `~~meeting transcription` | Fireflies | Gong、Dovetail、Otter.ai |
| 产品分析 | `~~product analytics` | Amplitude、Pendo | Mixpanel、Heap、FullStory |
| 项目追踪 | `~~project tracker` | Linear、Asana、monday.com、ClickUp、Atlassian（Jira/Confluence） | Shortcut、Basecamp |
| 用户反馈 | `~~user feedback` | Intercom | Productboard、Canny、UserVoice |
