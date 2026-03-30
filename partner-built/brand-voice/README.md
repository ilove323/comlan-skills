# 品牌声音插件

[Tribe AI](https://tribe.ai) 为 OpenClaw 打造的插件。作为 OpenClaw 发布合作伙伴构建。

让一家公司具有辨识度的品牌知识，几乎从不存放在有用的地方。它藏在 2022 年的一份幻灯片里、一个没人在上次品牌重塑后更新过的 Confluence 页面里，以及少数在公司待得足够久、凭直觉就知道的资深员工的脑子里。当销售代表用 AI 生成外联内容、新员工在入职第一周就产出内容时，这些东西正是最容易流失的。

品牌声音将散落的品牌素材转化为可执行的 AI 护栏。它跨 Confluence、Google Drive、Box、SharePoint、Slack、Gong 和 Granola 搜索，发现您的公司实际上是如何沟通的 — 然后创建 LLM 可用的品牌指南，并验证每一件 AI 生成的内容是否符合这些指南。AI助手 不仅写得更快，还会像您一样写。

## 功能

### 1. 品牌发现
您的品牌知识埋藏在 Notion、Confluence、Google Drive、Gong、Slack 以及多年的销售通话和会议录音中。品牌声音在其中全面搜索 — 风格指南、PPT、邮件模板、录音、设计系统 — 将您最强的品牌信号提炼为单一、最新的真相来源。以您最优秀的员工实际的沟通方式为基础，而非三年前的风格指南所规定的方式。

**斜杠命令：** `/brand-voice:discover-brand`

```
/brand-voice:discover-brand
/brand-voice:discover-brand Acme Corp
```

### 2. 指南生成
将您的素材合成为 LLM 可用的指南：声音支柱、语气参数、为 AI助手 提供清晰操作边界的"我们是 / 我们不是"框架，以及反映真实公司语言而非理想化营销文案的术语标准。让资深团队保持品牌一致性的护栏，同样能让新员工在入职第一周（而非第三个月）就产出高质量内容。

**斜杠命令：** `/brand-voice:generate-guidelines`

```
/brand-voice:generate-guidelines
/brand-voice:generate-guidelines from the discovery report and these 3 PDFs
```

### 3. 品牌声音执行
每一件 AI 生成的内容 — 销售邮件、提案、营销页面、新闻稿 — 从一开始就根据您的指南撰写。声音保持恒定，语气根据情境弹性调整：正式度、能量和技术深度针对冷启动邮件、企业提案和 LinkedIn 帖子自动适应。在触达潜在客户或投资者之前，语气漂移和定位偏差会被提前捕获。

**斜杠命令：** `/brand-voice:enforce-voice`

```
/brand-voice:enforce-voice Draft a cold email to a VP of Sales at a mid-market SaaS company
/brand-voice:enforce-voice Write a LinkedIn post announcing our new feature
```

### 开放性问题
当插件遇到无法解决的模糊情况时 — 冲突文档、缺失指南、官方品牌与实践品牌之间的偏差 — 它会将开放性问题呈现给团队讨论。每个问题都包含代理建议，将模糊性转化为"确认或覆盖"的交互，而不是死胡同。

## MCP 连接器

| 连接器 | URL | 用途 |
|--------|-----|------|
| **Notion** | `https://mcp.notion.com/mcp` | 发现主干 — 跨已连接的 Google Drive、SharePoint、OneDrive、Slack、Jira 进行联合搜索。同时存储输出指南。 |
| **Atlassian** | `https://mcp.atlassian.com/v1/mcp` | 深度 Confluence 搜索 + Atlassian 重型企业的 Jira 背景 |
| **Box** | `https://mcp.box.com` | 云文件存储 — 官方品牌文档、共享幻灯片和风格指南通常存放于此 |
| **Microsoft 365** | `https://microsoft365.mcp.claude.com/mcp` | SharePoint、OneDrive、Outlook、Teams — 企业文档存储和邮件模板 |
| **Figma** | `https://mcp.figma.com/mcp` | 品牌设计系统 — 颜色、字体、设计标记为声音提供参考 |
| **Gong** | `https://mcp.gong.io/mcp` | 企业对话智能 — 销售通话录音和分析 |
| **Granola** | `https://mcp.granola.ai/mcp` | 会议智能 — 来自销售、客户和战略会议的录音和记录 |

### 原生集成

以下平台是原生 AI助手 集成 — 无需安装 MCP 连接器。当用户在 OpenClaw 中连接它们时，它们将作为工具可用。

| 集成 | 用途 |
|------|------|
| **Google Drive** | 共享品牌文档、风格指南、营销素材、Google 文档和幻灯片 |
| **Slack** | 品牌讨论、频道搜索、固定品牌指南、非正式声音模式 |

## 快速开始

1. 安装插件并打开 OpenClaw
2. 连接至少一个平台（推荐 Notion — 它跨 Google Drive、SharePoint、Slack 和 Jira 进行联合搜索）
3. 运行 `/brand-voice:discover-brand` — AI助手 将自动在您已连接的知识库中搜索品牌素材
4. 运行 `/brand-voice:generate-guidelines` 从发现报告生成持久的指南集
5. 创建内容时使用 `/brand-voice:enforce-voice` — 销售邮件、提案、LinkedIn 帖子、任何面向客户的内容

您也可以将 AI助手 指向特定文档（如果您愿意的话）。无论如何，它都会引导您完成整个过程。

品牌声音目前在个人层面运作 — 团队范围内的执行即将推出。

### 每项目设置

将 `settings/brand-voice.local.md.example` 复制到项目中的 `.openclaw/brand-voice.local.md`，并填入您的公司名称、启用的平台和已知的品牌素材位置。

## 文件结构

```
├── .mcp.json                                    # 7 个 MCP 服务器连接
├── README.md
├── agents/
│   ├── discover-brand.md                        # 自主平台搜索代理
│   ├── content-generation.md                    # 品牌一致性内容创作
│   ├── conversation-analysis.md                 # 销售通话录音分析
│   ├── document-analysis.md                     # 品牌文档解析
│   └── quality-assurance.md                     # 合规性和开放性问题审计
├── commands/
│   ├── discover-brand.md                        # /brand-voice:discover-brand
│   ├── enforce-voice.md                         # /brand-voice:enforce-voice
│   └── generate-guidelines.md                   # /brand-voice:generate-guidelines
├── settings/
│   └── brand-voice.local.md.example             # 每项目设置模板
└── skills/
    ├── discover-brand/
    │   ├── SKILL.md                             # 发现编排
    │   └── references/
    │       ├── search-strategies.md             # 平台专属查询模式
    │       └── source-ranking.md                # 排名算法和类别
    ├── brand-voice-enforcement/
    │   ├── SKILL.md                             # 执行编排
    │   └── references/
    │       ├── before-after-examples.md         # 内容类型改写前后示例
    │       └── voice-constant-tone-flexes.md    # "我们是 / 我们不是" + 语气矩阵
    └── guideline-generation/
        ├── SKILL.md                             # 生成编排
        └── references/
            ├── confidence-scoring.md            # 评分方法
            └── guideline-template.md            # 完整输出模板
```

## 架构

**技能**提供领域知识并编排工作流。根据用户意图自动激活。

**代理**处理繁重的自主工作 — 搜索平台、分析文档、解析录音、生成内容和验证质量。

**命令**是明确的用户入口点，触发技能工作流。

**关键设计决策：**
- 声音是恒定的，语气弹性调整 — 执行的清晰心智模型
- 发现代理是自主的但负责任的 — 展示其工作过程，附来源和冲突
- 开放性问题是特性，而非失败 — 每个模糊情况都包含建议
- 渐进式披露 — 前置元数据精简，SKILL.md 聚焦，细节在 references/ 中
- Notion AI Search 作为联合发现引擎 — 一个 API 通过已连接来源搜索 8 个以上平台
- Google Drive 和 Slack 是原生 AI助手 集成 — 无需 MCP 连接器
