# 企业搜索

这是一款主要为 [OpenClaw](https://openclaw.ai) 设计的企业搜索插件——同时也适用于 Claude Code。在一个地方搜索公司所有工具——邮件、聊天、文档和 Wiki——无需在应用之间切换。

---

## 工作原理

一条查询同时搜索所有已连接的工具。AI助手分解您的问题，在每个来源运行针对性搜索，并将结果合成为带有来源归属的单一连贯答案。

```
您："我们就 API 重设计做了什么决定？"
              ↓ AI助手搜索
~~chat: 周二 #engineering 频道中包含决定的讨论
~~email: Sarah 发送的附有规格的跟进邮件
~~cloud storage: 昨天修改的 API 设计文档
              ↓ AI助手合成
"团队在周二决定采用 REST 而非 GraphQL。
 Sarah 在周四发送了更新的规格。设计文档
 反映了最终方案。"
```

无需切换标签页。无需记住哪个工具有什么内容。提问，获得答案。

---

## 搜索范围

> 如果您看到不熟悉的占位符或需要检查已连接的工具，请参阅 [CONNECTORS.md](CONNECTORS.md)。

连接任意组合的来源。连接越多，答案越完整。

| 来源 | 搜索内容 |
|--------|---------------|
| **~~chat** | 消息、讨论串、频道、私信 |
| **~~email** | 邮件、附件、对话 |
| **~~cloud storage** | 文档、表格、幻灯片、PDF |
| **Wiki / 知识库** | 内部文档、操作手册 |
| **项目管理** | 任务、议题、史诗、里程碑 |
| **CRM** | 客户、联系人、商机 |
| **工单系统** | 支持工单、客户问题 |

每个来源都是一个 MCP 连接。在 MCP 设置中添加更多来源以扩展搜索范围。

---

## 命令

| 命令 | 功能 |
|---------|--------------|
| `/search` | 在一条查询中搜索所有已连接来源 |
| `/digest` | 生成所有来源活动的每日或每周摘要 |

### 搜索

```
/enterprise-search:search Aurora 项目的状态是什么？
/enterprise-search:search from:sarah about:budget after:2025-01-01
/enterprise-search:search 本周 #product 中做的决定
```

支持过滤器：`from:`、`in:`、`after:`、`before:`、`type:` — 在每个来源的原生查询语法中智能应用。

### 摘要

```
/enterprise-search:digest --daily      # 今天所有来源发生了什么
/enterprise-search:digest --weekly     # 按项目/主题分组的每周汇总
```

突出显示行动项、决定和对您的提及。按主题分组活动，方便快速浏览重要内容。

---

## 技能

三个技能驱动搜索体验：

**搜索策略** — 查询分解和针对来源的转换。将您的自然语言问题分解为每个来源的针对性搜索，处理歧义，并在来源不可用时优雅降级。

**来源管理** — 了解哪些 MCP 来源可用，指导您连接新来源，管理来源优先级，并处理速率限制。

**知识合成** — 将来自多个来源的结果合并为连贯的答案。对跨来源信息进行去重，归属来源，根据时效性和权威性评估置信度，并对大结果集进行摘要。

---

## 示例工作流

### 查找决定

```
您：/enterprise-search:search 我们什么时候决定切换到 Postgres 的？

AI助手搜索：
  ~~chat → #engineering、#infrastructure 中关于 "postgres" "切换" "决定" 的内容
  ~~email → 主题中包含 "postgres" 的讨论串
  ~~cloud storage → 提到数据库迁移的文档

结果："决定于 3 月 3 日在 #infrastructure 做出（链接）。
       Sarah 的 3 月 4 日邮件确认了时间表。
       迁移计划文档于 3 月 5 日更新。"
```

### 休假后跟进

```
您：/enterprise-search:digest --weekly

AI助手扫描：
  ~~chat → 您所在的频道、私信、提及
  ~~email → 收件箱活动
  ~~cloud storage → 与您共享或修改的文档

结果：按项目分组的摘要，标记了行动项
       并突出显示了决定。
```

### 查找专家

```
您：/enterprise-search:search 谁了解我们的 Kubernetes 设置？

AI助手搜索：
  ~~chat → 关于 Kubernetes、k8s、集群的消息
  ~~cloud storage → 关于基础设施的文档作者
  Wiki → 操作手册和架构文档

结果："根据消息历史和文档作者身份，
       Alex 和 Priya 是您的 k8s 联系人。
       这是主要操作手册（链接）。"
```

---

## 快速开始

```bash
# 1. 安装
openclaw skills install enterprise-search

# 2. 搜索所有内容
/enterprise-search:search [您的问题]

# 3. 获取摘要
/enterprise-search:digest --daily
```

通过 MCP 连接的来源越多，搜索结果越完整。先从 ~~chat、~~email 和 ~~cloud storage 开始，然后根据需要添加 Wiki、项目管理工具和 CRM。

---

## 设计理念

知识工作者每周花费数小时寻找散落在各工具中的信息。答案确实存在于某处——在 Slack 讨论串、邮件链、文档或 Wiki 页面中——但找到它意味着逐一搜索每个工具，对比结果，并希望检查了正确的地方。

企业搜索将您所有的工具视为一个可搜索的知识库。一条查询，所有来源，合成结果。公司的知识不应该被锁在信息孤岛中。一次搜索所有内容。
