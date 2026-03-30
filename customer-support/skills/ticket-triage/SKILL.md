---
name: ticket-triage
description: 对支持工单或客户问题进行分类和优先级排序。适用于新工单需要分类、分配 P1-P4 优先级、决定由哪个团队处理，或在路由前检查是否为重复问题或已知问题等情况。触发词：工单分类、工单处理、ticket triage
---

# /ticket-triage

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

对传入的支持工单或客户问题进行分类、排优先级和路由。输出结构化的分类评估以及建议的初始回复。

## 用法

```
/ticket-triage <ticket text, customer message, or issue description>
```

示例：
- `/ticket-triage Customer says their dashboard has been showing a blank page since this morning`
- `/ticket-triage "I was charged twice for my subscription this month"`
- `/ticket-triage User can't connect their SSO — getting a 403 error on the callback URL`
- `/ticket-triage Feature request: they want to export reports as PDF`

## 工作流程

### 1. 解析问题

阅读输入并提取：

- **核心问题**：客户实际遇到了什么？
- **症状**：他们看到了什么具体行为或错误？
- **客户背景**：这是谁？是否有账户详情、套餐级别或历史记录？
- **紧急信号**：他们是否被完全阻塞？这是生产环境吗？有多少用户受影响？
- **情绪状态**：沮丧、困惑、平静陈述、正在升级？

### 2. 分类和排优先级

使用以下类别分类法和优先级框架：

- 分配**主类别**（Bug、操作指南、功能请求、计费、账户、集成、安全、数据、性能）和可选的次类别
- 基于影响和紧急程度分配**优先级**（P1-P4）
- 识别问题对应的**产品区域**

### 3. 检查重复和已知问题

路由前，检查可用来源：

- **~~support platform**：搜索类似的已开放或近期解决的工单
- **~~knowledge base**：检查已知问题或现有文档
- **~~project tracker**：检查是否有现有 Bug 报告或功能请求

应用以下重复检测流程。

### 4. 确定路由

根据以下路由规则，基于类别和复杂度，建议由哪个团队或队列处理。

### 5. 生成分类输出

```
## Triage: [One-line issue summary]

**Category:** [Primary] / [Secondary if applicable]
**Priority:** [P1-P4] — [Brief justification]
**Product area:** [Area/team]

### Issue Summary
[2-3 sentence summary of what the customer is experiencing]

### Key Details
- **Customer:** [Name/account if known]
- **Impact:** [Who and what is affected]
- **Workaround:** [Available / Not available / Unknown]
- **Related tickets:** [Links to similar issues if found]
- **Known issue:** [Yes — link / No / Checking]

### Routing Recommendation
**Route to:** [Team or queue]
**Why:** [Brief reasoning]

### Suggested Initial Response
[Draft first response to the customer — acknowledge the issue,
set expectations, provide workaround if available.
Use the auto-response templates below as a starting point.]

### Internal Notes
- [Any additional context for the agent picking this up]
- [Reproduction hints if it's a bug]
- [Escalation triggers to watch for]
```

### 6. 提供后续步骤

展示分类结果后：
- "需要我为客户起草完整回复吗？"
- "需要我搜索关于此问题的更多背景吗？"
- "需要我检查这是否是跟踪器中的已知 Bug 吗？"
- "需要升级吗？我可以用 /customer-escalation 打包升级材料。"

---

## 类别分类法

为每个工单分配**主类别**，可选**次类别**：

| 类别 | 说明 | 信号词 |
|------|------|--------|
| **Bug** | 产品行为不正确或出乎意料 | 错误、损坏、崩溃、不工作、出乎意料、有误、失败 |
| **操作指南** | 客户需要关于如何使用产品的指导 | 怎么、能否、在哪里、设置、配置、帮助 |
| **功能请求** | 客户想要不存在的功能 | 如果能……就好了、希望能、有计划吗、请求 |
| **计费** | 付款、订阅、发票或定价问题 | 收费、发票、付款、订阅、退款、升级、降级 |
| **账户** | 账户访问、权限、设置或用户管理 | 登录、密码、访问、权限、SSO、被锁定、无法登录 |
| **集成** | 与第三方工具或 API 的连接问题 | API、Webhook、集成、连接、OAuth、同步、第三方 |
| **安全** | 安全顾虑、数据访问或合规问题 | 数据泄露、未授权、合规、GDPR、SOC 2、漏洞 |
| **数据** | 数据质量、迁移、导入/导出问题 | 数据丢失、导出、导入、迁移、数据有误、重复 |
| **性能** | 速度、可靠性或可用性问题 | 慢、超时、延迟、宕机、不可用、降级 |

### 类别判断技巧

- 如果客户**同时**反映了 Bug 和功能请求，Bug 是主类别
- 如果由于 Bug 无法登录，类别是 **Bug**（非账户）——根本原因决定类别
- "以前能用，现在不能了" = **Bug**
- "我想让它以不同方式工作" = **功能请求**
- "怎么让它工作？" = **操作指南**
- 有疑问时，倾向于 **Bug**——调查比忽略要好

## 优先级框架

### P1 — 紧急
**标准：** 生产系统宕机、数据丢失或损坏、安全漏洞、所有或大多数用户受影响。

- 客户完全无法使用产品
- 数据正在丢失、损坏或泄露
- 安全事件正在发生
- 问题正在恶化或扩大范围

**SLA 预期：** 1 小时内响应。持续处理直到解决或缓解。每 1-2 小时更新。

### P2 — 高
**标准：** 主要功能损坏、重大工作流被阻塞、多用户受影响、无变通方案。

- 核心工作流损坏但产品部分可用
- 多个用户受影响或关键账户受到影响
- 问题阻塞时间敏感的工作
- 没有合理的变通方案

**SLA 预期：** 4 小时内响应。当天主动调查。每 4 小时更新。

### P3 — 中
**标准：** 功能部分损坏，有变通方案，单一用户或小团队受影响。

- 功能工作不正常但有变通方案
- 问题造成不便但未阻塞关键工作
- 单一用户或小团队受影响
- 客户没有紧急升级

**SLA 预期：** 1 个工作日内响应。3 个工作日内解决或更新。

### P4 — 低
**标准：** 小问题、界面缺陷、一般问题、功能请求。

- 不影响功能的界面或 UI 问题
- 功能请求和改进想法
- 一般问题或操作指南咨询
- 有简单、已记录解决方案的问题

**SLA 预期：** 2 个工作日内响应。按正常进度解决。

### 优先级升级触发器

以下情况自动提升优先级：
- 客户等待时间超过 SLA 允许
- 多个客户报告相同问题（发现规律）
- 客户明确升级或提及高管介入
- 原本有效的变通方案停止工作
- 问题扩大范围（更多用户、更多数据、新症状）

## 路由规则

根据类别和复杂度路由工单：

| 路由至 | 时机 |
|--------|------|
| **一线（前台支持）** | 操作指南问题、有已记录解决方案的已知问题、计费咨询、密码重置 |
| **二线（高级支持）** | 需要调查的 Bug、复杂配置、集成故障排查、账户问题 |
| **工程** | 需要代码修复的已确认 Bug、基础设施问题、性能下降 |
| **产品** | 有大量需求的功能请求、设计决策、工作流缺口 |
| **安全** | 数据访问顾虑、漏洞报告、合规问题 |
| **财务/计费** | 退款请求、合同争议、复杂的计费调整 |

## 重复检测

路由前，检查重复：

1. **按症状搜索**：查找有类似错误信息或描述的工单
2. **按客户搜索**：检查该客户是否有同一问题的开放工单
3. **按产品区域搜索**：查找同一功能区域的近期工单
4. **检查已知问题**：与已记录的已知问题比较

**如果发现重复：**
- 将新工单链接到现有工单
- 通知客户这是正在跟踪的已知问题
- 将新报告中的任何新信息添加到现有工单
- 如果新报告增加了紧急性（更多客户受影响等），提升优先级

## 按类别的自动回复模板

### Bug — 初始回复
```
Thank you for reporting this. I can see how [specific impact]
would be disruptive for your work.

I've logged this as a [priority] issue and our team is
investigating. [If workaround exists: "In the meantime, you
can [workaround]."]

I'll update you within [SLA timeframe] with what we find.
```

### 操作指南 — 初始回复
```
Great question! [Direct answer or link to documentation]

[If more complex: "Let me walk you through the steps:"]
[Steps or guidance]

Let me know if that helps, or if you have any follow-up
questions.
```

### 功能请求 — 初始回复
```
Thank you for this suggestion — I can see why [capability]
would be valuable for your workflow.

I've documented this and shared it with our product team.
While I can't commit to a specific timeline, your feedback
directly informs our roadmap priorities.

[If alternative exists: "In the meantime, you might find
[alternative] helpful for achieving something similar."]
```

### 计费 — 初始回复
```
I understand billing issues need prompt attention. Let me
look into this for you.

[If straightforward: resolution details]
[If complex: "I'm reviewing your account now and will have
an answer for you within [timeframe]."]
```

### 安全 — 初始回复
```
Thank you for flagging this — we take security concerns
seriously and are reviewing this immediately.

I've escalated this to our security team for investigation.
We'll follow up with you within [timeframe] with our findings.

[If action is needed: "In the meantime, we recommend
[protective action]."]
```

## 分类最佳实践

1. 在分类前阅读完整工单——后续消息中的背景往往会改变评估
2. 按**根本原因**分类，而非只看描述的症状
3. 对优先级有疑问时，倾向于更高优先级——降级比错过 SLA 容易
4. 路由前始终检查重复和已知问题
5. 写内部备注帮助接手的人快速了解背景
6. 记录您已检查或排除的内容，避免重复调查
7. 标记规律——如果您反复看到同一问题，即使单个工单优先级低，也要升级该规律
