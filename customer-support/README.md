# 客户支持插件

一款客户支持插件，主要为 [OpenClaw](https://openclaw.ai) 设计，同时也支持在 Claude Code 中使用。为支持团队提供工单分类、升级管理、回复起草、客户调研和知识库撰写功能。

## 安装

```
openclaw skills install customer-support
```

或手动复制到：

```bash
~/.openclaw/skills/customer-support
```

## 功能说明

本插件将 AI助手 变成客户支持的协作伙伴，帮助您：

- **分类工单** — 提供结构化分类、优先级评估和路由建议
- **调研客户问题** — 整合多来源信息，附带置信度评分
- **起草专业回复** — 根据情况、紧急程度和沟通渠道量身定制
- **打包升级材料** — 为工程或产品团队提供完整背景、复现步骤和业务影响
- **撰写知识库文章** — 从已解决问题中沉淀知识，减少未来的工单量

## 命令

| 命令 | 说明 |
|---|---|
| `/triage` | 对支持工单或客户问题进行分类、排优先级和路由 |
| `/research` | 对客户问题或话题进行多来源调研 |
| `/draft-response` | 起草任意场景的面向客户回复 |
| `/escalate` | 为工程、产品或管理层打包升级材料 |
| `/kb-article` | 根据已解决问题起草知识库文章 |

## 技能

| 技能 | 说明 |
|---|---|
| `ticket-triage` | 分类分类法、优先级框架（P1-P4）、路由规则、重复检测 |
| `customer-research` | 多来源调研方法论、来源优先级、答案综合 |
| `response-drafting` | 沟通最佳实践、语气指南、常见场景模板 |
| `escalation` | 升级层级、结构化升级格式、影响评估、跟进节奏 |
| `knowledge-management` | 文章结构规范、面向搜索优化的写作、审阅周期、维护 |

## 数据来源

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](CONNECTORS.md)。

连接您的支持平台、知识库和沟通工具以获得最佳体验。如果未连接，请手动提供客户背景信息。

**已包含的 MCP 连接：**
- 即时通讯（Slack）用于内部讨论和客户频道背景
- 支持平台（Intercom）用于工单历史和客户对话
- CRM（HubSpot）用于账户详情和联系人信息
- 知识库（Guru、Notion）用于内部文档和操作手册
- 项目跟踪（Atlassian）用于 Bug 报告和功能请求
- 邮件和云存储（Microsoft 365）用于客户往来邮件

**更多选项：**
- 请参阅 [CONNECTORS.md](CONNECTORS.md) 了解每个类别的备选工具

## 使用示例

### 分类新工单

```
您：/triage Customer says their dashboard has been showing a blank
     page since this morning — they're on the Enterprise plan

AI助手：正在分析问题...

## 工单分类：Dashboard 空白页 — 企业版客户

**类别：** Bug
**优先级：** P2 — 高（核心功能中断，企业版客户）
**产品区域：** Dashboard / 前端

### 路由建议
路由至：二线支持（调查浏览器/账户具体情况，如可复现则升级至工程）

### 建议初始回复
[草稿内容：确认问题，询问浏览器和近期变动的澄清问题，提供状态页链接]
```

### 调研客户问题

```
您：/research Does our platform support SSO with Okta?

AI助手：正在检索所有可用来源...

[搜索文档、知识库、历史对话和网络来源，综合答案并附置信度和来源标注]
```

### 打包升级材料

```
您：/escalate API returning 500 errors intermittently — 3
     Enterprise customers reported this week

AI助手：正在收集背景并构建升级简报...

[拉取工单历史、识别受影响账户、整理复现步骤、评估业务影响，生成结构化工程升级材料]
```

### 起草回复

```
您：/draft-response Customer escalation — their integration
     has been down for 2 days and they're frustrated

AI助手：[生成充满同理心的专业回复，承认客户的挫败感，主动承担责任，提供含时间线的明确行动计划，并提出致电]
```

### 撰写知识库文章

```
您：/kb-article How to configure webhook notifications —
     just resolved this for the third customer this month

AI助手：[生成含前置条件、分步操作、验证步骤和常见问题的结构化操作指南，针对搜索优化]
```

## 配置

本插件开箱即用，已包含 MCP 连接。如需更丰富的体验，可通过 OpenClaw 设置连接更多数据源：

1. **支持平台**：添加您的工单系统以获取工单历史和客户背景
2. **知识库**：添加您的 Wiki 以获取内部文档和现有知识库文章
3. **项目跟踪**：添加您的问题跟踪器以获取 Bug 报告和功能请求
4. **CRM**：添加您的 CRM 以获取账户详情和联系人信息

如果未连接上述工具，插件会要求您手动提供背景信息，并提供可填入自有数据的框架和模板。
