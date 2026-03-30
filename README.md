# 知识工作插件集

将 AI 助手变成你所在角色、团队和公司的专家。适配 [OpenClaw](https://openclaw.ai/)，同样兼容 Claude Code。

## 为什么需要插件

插件让你能够进一步定制：告诉 AI 你偏好的工作方式、从哪些工具和数据中提取信息、如何处理关键工作流，以及开放哪些斜杠命令——让你的团队获得更好、更一致的输出。

每个插件都为特定岗位职能捆绑了技能（skills）、连接器（connectors）、斜杠命令和子代理。开箱即用，为该角色提供强大的起点。真正的价值在于将其定制为你公司的实际情况——你的工具、你的术语、你的流程。

## 插件目录

| 插件 | 功能简介 | 主要连接器 |
|------|----------|-----------|
| **[productivity](./productivity)** | 任务管理、日历、日常工作流和个人上下文管理 | Slack、Notion、Asana、Linear、Jira |
| **[sales](./sales)** | 客户研究、通话准备、管道复盘、起草外联、竞争情报 | Slack、HubSpot、Close、Clay、ZoomInfo |
| **[customer-support](./customer-support)** | 工单分类、回复起草、问题升级、知识库文章 | Slack、Intercom、HubSpot、Guru、Jira |
| **[product-management](./product-management)** | 写spec、规划路线图、合成用研、干系人沟通、竞品跟踪 | Slack、Linear、Jira、Notion、Figma、Amplitude |
| **[marketing](./marketing)** | 内容创作、活动策划、品牌审查、竞品简报、效果报告 | Slack、Figma、HubSpot、Amplitude、Notion |
| **[legal](./legal)** | 合同审查、NDA分类、合规检查、风险评估、会议简报 | Slack、Box、Egnyte、Jira、Microsoft 365 |
| **[finance](./finance)** | 凭证准备、账户对账、财务报表、差异分析、结账管理 | Snowflake、Databricks、BigQuery、Slack |
| **[human-resources](./human-resources)** | 招聘管道、入职规划、绩效评估、薪酬分析、政策查询 | Slack、Notion、Jira、Microsoft 365 |
| **[engineering](./engineering)** | 代码审查、架构决策、调试、部署检查、故障响应 | Slack、GitHub、Linear、Jira、Notion |
| **[design](./design)** | 设计评审、无障碍检查、设计交接、设计系统、UX文案 | Slack、Figma、Linear、Jira、Notion |
| **[operations](./operations)** | 流程文档、合规跟踪、风险评估、供应商管理、状态报告 | Slack、Notion、Jira、Asana |
| **[data](./data)** | SQL查询、数据可视化、探索分析、仪表板、数据验证 | Snowflake、Databricks、BigQuery、Amplitude |
| **[enterprise-search](./enterprise-search)** | 跨邮件、聊天、文档和Wiki的统一企业搜索 | Slack、Notion、Guru、Jira、Microsoft 365 |
| **[pdf-viewer](./pdf-viewer)** | PDF查看、注释、表单填写和签名 | — |
| **[bio-research](./bio-research)** | 文献搜索、基因组分析、靶点优先级排序，加速生命科学早期研发 | PubMed、BioRender、ChEMBL、Synapse、Benchling |
| **[cowork-plugin-management](./cowork-plugin-management)** | 创建新插件或为组织定制现有插件 | — |
| **[brand-voice](./partner-built/brand-voice)** | Tribe AI | 发现品牌素材、生成品牌指南、执行AI内容品牌一致性 |
| **[apollo](./partner-built/apollo)** | — | Apollo.io 线索丰富、潜客挖掘和序列管理 |
| **[common-room](./partner-built/common-room)** | — | Common Room 账户研究、通话准备和潜客管理 |
| **[slack](./partner-built/slack)** | — | Slack 频道搜索、消息阅读和内容创作 |

## 安装

### OpenClaw

```bash
# 从 ClawHub 安装单个插件
openclaw skills install <plugin-name>

# 或手动复制到本地 skills 目录
cp -r product-management ~/.openclaw/skills/product-management
```

安装后，技能（skills）会在相关场景自动激活，斜杠命令可在会话中直接使用（如 `/write-spec`、`/code-review`）。

### MCP 连接器配置

每个插件目录下都有 `MCP_SETUP.md`，说明如何在 `~/.openclaw/openclaw.json` 中配置对应的 MCP 服务器。

## 插件结构

每个插件遵循统一结构：

```
plugin-name/
├── openclaw.plugin.json         # 插件清单
├── .mcp.json                    # MCP 服务器配置参考
├── MCP_SETUP.md                 # OpenClaw MCP 配置指南（中文）
├── README.md                    # 插件说明
├── CONNECTORS.md                # 连接器说明
├── commands/                    # 斜杠命令（显式触发）
└── skills/                      # 领域知识技能（自动激活）
```

- **Skills**：编码了领域专业知识、最佳实践和分步工作流，AI 在相关场景下自动调用。
- **Commands**：显式触发的操作（如 `/brainstorm`、`/code-review`）。
- **Connectors**：通过 [MCP 协议](https://modelcontextprotocol.io/) 将 AI 连接到你的工具栈。

所有组件均为文件形式——Markdown 和 JSON，无需代码、无需基础设施、无需构建步骤。

## 中文触发词

每个技能的 `description` 字段包含中文触发词，可直接用中文描述任务来调用：

- 「帮我写一个产品需求文档」→ 自动激活 `write-spec`
- 「帮我做代码审查」→ 自动激活 `code-review`
- 「帮我分析这份数据」→ 自动激活 `analyze`
- 「帮我起草一封外联邮件」→ 自动激活 `draft-outreach`

## 定制化

这些插件是通用起点。针对公司实际情况定制后效果大幅提升：

- **替换连接器** — 编辑 `.mcp.json` 指向你的具体工具栈。
- **添加公司上下文** — 在 skill 文件中补充你的术语、组织架构和流程。
- **调整工作流** — 按照团队的实际做法修改 skill 指令。
- **创建新插件** — 使用 `cowork-plugin-management` 插件或参照上述结构为未覆盖的角色构建新插件。

## 贡献

插件全部是 Markdown 文件。Fork 仓库，进行修改，提交 PR 即可。
