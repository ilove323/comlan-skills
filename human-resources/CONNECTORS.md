# 连接器说明

## 工具引用的工作原理

插件文件使用 `~~类别` 作为占位符，代表用户在该类别中连接的任何工具。例如，`~~HRIS` 可能指Workday、BambooHR或任何其他具有MCP服务器的HRIS系统。

插件**与具体工具无关**——以类别（HRIS、ATS、邮件等）而非特定产品来描述工作流程。`.mcp.json` 预配置了特定MCP服务器，但该类别中的任何MCP服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他可选 |
|------|-------|------------|---------|
| ATS | `~~ATS` | — | Greenhouse、Lever、Ashby、Workable |
| 日历 | `~~calendar` | Google Calendar | Microsoft 365 |
| 团队协作 | `~~chat` | Slack | Microsoft Teams |
| 邮件 | `~~email` | Gmail、Microsoft 365 | — |
| HRIS | `~~HRIS` | — | Workday、BambooHR、Rippling、Gusto |
| 知识库 | `~~knowledge base` | Notion、Atlassian（Confluence） | Guru、Coda |
| 薪酬数据 | `~~compensation data` | — | Pave、Radford、Levels.fyi |
