# 知识MCP搜索策略

插件定制过程中收集组织上下文的查询模式。

## 查找工具名称

**源代码管理：**
- 搜索："GitHub" 或 "GitLab" 或 "Bitbucket"
- 搜索："pull request" 或 "merge request"
- 查找：仓库链接、CI/CD提及

**项目管理：**
- 搜索："Asana" 或 "Jira" 或 "Linear" 或 "Monday"
- 搜索："sprint" 与 "tickets"
- 查找：任务链接、项目看板提及

**即时通讯：**
- 搜索："Slack" 或 "Teams" 或 "Discord"
- 查找：频道提及、集成讨论

**数据分析：**
- 搜索："Datadog" 或 "Grafana" 或 "Mixpanel"
- 搜索："monitoring" 或 "observability"（监控/可观测性）
- 查找：仪表盘链接、告警配置

**设计：**
- 搜索："Figma" 或 "Sketch" 或 "Adobe XD"
- 查找：设计文件链接、交付讨论

**CRM：**
- 搜索："Salesforce" 或 "HubSpot"
- 查找：商机提及、客户记录链接

## 查找组织配置值

**工作区/项目ID：**
- 搜索现有集成或收藏的链接
- 查找管理员/配置文档

**团队惯例：**
- 搜索："story points" 或 "estimation"（故事点/估算）
- 搜索："workflow" 或 "ticket status"（工作流/工单状态）
- 查找工程流程文档

**频道/团队名称：**
- 搜索："standup" 或 "engineering" 或 "releases"（站会/工程/发版）
- 查找频道命名模式

## 知识MCP不可用时

若未配置知识MCP，跳过自动发现，直接对所有类别使用AskUserQuestion。注意：AskUserQuestion始终包含"跳过"按钮和自由文本输入框，因此不要将"无"或"其他"作为选项。
