# 销售插件

一款销售效率插件，主要为 [OpenClaw](https://openclaw.ai) 设计，同时也支持在 Claude Code 中使用。帮助您快速开发客户、撰写外联邮件、管理销售管道、准备通话以及制定交易策略。适用于任何销售团队——单独搭配网络搜索和您自己的信息即可使用，接入 CRM、邮件及其他工具后效果更佳。

## 安装

```bash
openclaw skills install sales
```

或手动复制到：

```bash
~/.openclaw/skills/sales
```

## 命令

通过斜杠命令调用的明确工作流：

| 命令 | 说明 |
|---|---|
| `/call-summary` | 处理通话笔记或录音文字稿——提取行动项、起草跟进邮件、生成内部摘要 |
| `/forecast` | 生成加权销售预测——上传 CSV 或描述您的管道，设置配额，获取预测结果 |
| `/pipeline-review` | 分析管道健康状况——优先排序交易、标记风险、获取每周行动计划 |

所有命令均可**独立使用**（粘贴笔记、上传 CSV 或描述您的情况），接入 MCP 连接器后效果更强。

## 技能

AI助手在相关情境下自动调用的领域知识：

| 技能 | 说明 |
|---|---|
| `account-research` | 调研公司或人员——通过网络搜索获取公司情报、关键联系人、近期新闻、招聘信号 |
| `call-prep` | 准备销售通话——账户背景、与会者调研、建议议程、探索性问题 |
| `daily-briefing` | 优先排序的每日销售简报——会议安排、管道预警、邮件优先级、建议行动 |
| `draft-outreach` | 调研优先的外联——先调研潜在客户，再起草个性化邮件和 LinkedIn 消息 |
| `competitive-intelligence` | 调研竞争对手——产品对比、定价情报、近期发布、差异化矩阵、销售话术 |
| `create-an-asset` | 生成定制销售资料——针对潜在客户量身定制的落地页、PPT、单页纸、工作流演示 |

## 使用示例

### 通话结束后

```
/call-summary
```

粘贴您的笔记或文字稿，获取结构化摘要、带负责人的行动项以及草稿跟进邮件。若已连接 CRM，可自动记录活动并创建任务。

### 每周预测

```
/forecast
```

上传 CRM 导出的 CSV（或粘贴您的交易列表），告知配额和时间节点，获取含最佳/可能/最差情景的加权预测、承诺与上行空间分解以及差距分析。

### 管道复盘

```
/pipeline-review
```

上传 CSV 或描述您的管道，获取健康评分、交易优先排序、风险标记（陈旧交易、过期关闭日期、单线程联系）以及每周行动计划。

### 调研潜在客户

只需自然提问：
```
Research Acme Corp before my call tomorrow
```

`account-research` 技能自动触发，提供公司概览、关键联系人、近期新闻及推荐接触策略。

### 起草外联邮件

```
Draft an email to the VP of Engineering at TechStart
```

`draft-outreach` 技能先调研潜在客户，再生成带多个角度的个性化外联邮件。

### 竞情分析

```
How do we compare to Competitor X?
```

`competitive-intelligence` 技能调研两家公司，建立差异化矩阵并附上销售话术。

## 独立使用与增强使用

每个命令和技能无需任何集成即可运行：

| 可完成的任务 | 独立使用 | 接入工具后增强 |
|--------------|----------|----------------|
| 处理通话笔记 | 粘贴笔记/文字稿 | 转录 MCP（如 Gong、Fireflies） |
| 预测管道 | 上传 CSV、粘贴交易 | CRM MCP |
| 复盘管道 | 上传 CSV、描述交易 | CRM MCP |
| 调研潜在客户 | 网络搜索 | 数据富化 MCP（如 Clay、ZoomInfo） |
| 准备通话 | 描述会议 | CRM、邮件、日历 MCP |
| 起草外联 | 网络搜索 + 您的背景信息 | CRM、邮件 MCP |
| 竞情分析 | 网络搜索 | CRM（赢/输数据）、文档（竞品手册） |

## MCP 集成

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](CONNECTORS.md)。

连接您的工具获得更丰富的体验：

| 类别 | 示例 | 功能 |
|---|---|---|
| **CRM** | HubSpot、Close | 管道数据、账户历史、联系人记录 |
| **转录** | Fireflies、Gong、Chorus | 通话录音、文字稿、关键时刻 |
| **数据富化** | Clay、ZoomInfo、Apollo | 公司与联系人数据富化 |
| **即时通讯** | Slack、Teams | 内部讨论、同事情报 |

请参阅 [CONNECTORS.md](CONNECTORS.md) 获取完整集成列表，包括邮件、日历及更多 CRM 选项。

## 设置

创建 `settings.local.json` 文件进行个性化配置：

- **OpenClaw**: 将文件保存在您与 OpenClaw 共享的任意文件夹中（通过文件夹选择器），插件会自动找到它。
- **OpenClaw**: 将文件保存在 `sales/.openclaw/settings.local.json`。

```json
{
  "name": "您的姓名",
  "title": "客户经理",
  "company": "您的公司",
  "quota": {
    "annual": 1000000,
    "quarterly": 250000
  },
  "product": {
    "name": "您的产品",
    "value_props": [
      "核心价值主张 1",
      "核心价值主张 2"
    ],
    "competitors": [
      "竞争对手 A",
      "竞争对手 B"
    ]
  }
}
```

如果未配置此信息，插件会以互动方式向您询问。
