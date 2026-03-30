# 连接器

## 工具引用的工作原理

插件文件使用 `~~category` 作为用户在该类别中连接的任何工具的占位符。例如，`~~design tool` 可能代表 Figma、Sketch 或任何其他具有 MCP 服务器的设计工具。

插件是**工具无关的**——它们以类别（设计工具、项目追踪器、用户反馈等）而非具体产品来描述工作流。`.mcp.json` 预配置了特定的 MCP 服务器，但该类别中的任何 MCP 服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 内置服务器 | 其他选项 |
|----------|-------------|-----------------|---------------|
| 聊天工具 | `~~chat` | Slack | Microsoft Teams |
| 设计工具 | `~~design tool` | Figma | Sketch、Adobe XD、Framer |
| 知识库 | `~~knowledge base` | Notion | Confluence、Guru、Coda |
| 项目追踪器 | `~~project tracker` | Linear、Asana、Atlassian (Jira/Confluence) | Shortcut、ClickUp |
| 用户反馈 | `~~user feedback` | Intercom | Productboard、Canny、UserVoice、Dovetail |
| 产品分析 | `~~product analytics` | — | Amplitude、Mixpanel、Heap、FullStory |
