---
name: draft-response
description: 起草根据情况和关系量身定制的专业面向客户回复。适用于回答产品问题、回应升级或故障、传达延期或无法修复等坏消息、拒绝功能请求，或回复计费问题等情况。触发词：起草回复、写客服回复、draft response
---

# /draft-response

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

起草专业的、面向客户的回复，根据情况、客户关系和沟通背景量身定制。

## 用法

```
/draft-response <context about the customer question, issue, or request>
```

示例：
- `/draft-response Acme Corp is asking when the new dashboard feature will ship`
- `/draft-response Customer escalation — their integration has been down for 2 days`
- `/draft-response Responding to a feature request we won't be building`
- `/draft-response Customer hit a billing error and wants a resolution ASAP`

## 工作流程

### 1. 理解背景

解析用户输入，确定：

- **客户**：沟通对象是谁？如果可以，查找账户背景。
- **情况类型**：问题、问题反馈、升级、公告、谈判、坏消息、好消息、跟进
- **紧急程度**：是否时间紧迫？客户等待多久了？
- **渠道**：邮件、支持工单、即时通讯或其他（相应调整正式程度）
- **关系阶段**：新客户、老客户、沮丧/已升级
- **干系人级别**：终端用户、经理、高管、技术人员、业务人员

### 2. 调研背景

从可用来源收集相关背景信息：

**~~email：**
- 与该客户关于此话题的过往往来邮件
- 此前分享的任何承诺或时间线
- 现有邮件线程的语气和风格

**~~chat：**
- 关于该客户或话题的内部讨论
- 来自产品、工程或管理层的任何指导意见
- 类似情况及其处理方式

**~~CRM（如已连接）：**
- 账户详情和套餐级别
- 联系人信息和关键干系人
- 过往升级或敏感问题

**~~support platform（如已连接）：**
- 相关工单及其解决情况
- 已知问题或变通方案
- SLA 状态和回复时间承诺

**~~knowledge base：**
- 可供参考的官方文档或帮助文章
- 产品路线图信息（如可对外分享）
- 政策或流程文档

### 3. 生成草稿

针对情况生成量身定制的回复：

```
## Draft Response

**To:** [Customer contact name]
**Re:** [Subject/topic]
**Channel:** [Email / Ticket / Chat]
**Tone:** [Empathetic / Professional / Technical / Celebratory / Candid]

---

[Draft response text]

---

### Notes for You (internal — do not send)
- **Why this approach:** [Rationale for tone and content choices]
- **Things to verify:** [Any facts or commitments to confirm before sending]
- **Risk factors:** [Anything sensitive about this response]
- **Follow-up needed:** [Actions to take after sending]
- **Escalation note:** [If this should be reviewed by someone else first]
```

### 4. 质量检查

呈现草稿前验证：

- [ ] 语气与情况和关系相符
- [ ] 没有超出授权范围的承诺
- [ ] 没有不应对外分享的产品路线图详情
- [ ] 对过往对话的引用准确
- [ ] 下一步和责任归属清晰
- [ ] 适合干系人级别（对高管不过于技术性，对工程师不过于模糊）
- [ ] 长度适合渠道（即时通讯简短，邮件稍长）

### 5. 提供迭代

展示草稿后：
- "需要我调整语气吗？（更正式、更随意、更有同理心、更直接）"
- "需要添加或删除某些具体内容吗？"
- "需要更短/更长的版本吗？"
- "需要我为不同干系人起草一个版本吗？"
- "需要我同时起草内部升级备注吗？"
- "需要我准备一封 [X 天] 后如未回复发送的跟进消息吗？"

---

## 客户沟通最佳实践

### 核心原则

1. **先表达同理心**：在给出解决方案之前，先承认客户的处境
2. **直接**：直奔主题——客户很忙。先说结论。
3. **诚实**：不要过度承诺、不要误导、不要用行话掩盖坏消息
4. **具体**：使用具体的细节、时间线和姓名——避免模糊表述
5. **承担责任**：在适当时候承担责任。用"我们"而非"系统"或"流程"
6. **形成闭环**：每封回复都应有明确的下一步或行动号召
7. **匹配对方的情绪**：如果对方沮丧，先表达同理心。如果对方兴奋，表现出热情。

### 回复结构

对于大多数客户沟通，遵循以下结构：

```
1. 确认/背景（1-2 句话）
   - 承认他们所说、所问或所经历的内容
   - 表明您理解他们的处境

2. 核心信息（1-3 段）
   - 传达主要信息、答复或更新
   - 具体且有实质内容
   - 包含他们需要的相关细节

3. 下一步（1-3 条）
   - 您将做什么以及何时做
   - 他们需要做什么（如有）
   - 他们下次何时会收到您的消息

4. 结尾（1 句话）
   - 温暖但专业的结束语
   - 强调您随时可以提供帮助
```

### 长度指南

- **即时通讯/IM**：1-4 句话。立即说重点。
- **支持工单回复**：1-3 个短段落。结构清晰，易于扫读。
- **邮件**：最多 3-5 段。尊重对方的收件箱。
- **升级回复**：根据需要，但结构良好，使用标题。
- **高管沟通**：越短越好。最多 2-3 段。以数据为导向。

## 语气和风格指南

### 语气范围

| 情况 | 语气 | 特点 |
|------|------|------|
| 好消息/成果 | 庆贺 | 热情、温暖、祝贺、展望未来 |
| 常规更新 | 专业 | 清晰、简洁、信息充分、友好 |
| 技术回复 | 精确 | 准确、详细、有条理、耐心 |
| 延期交付 | 负责 | 诚实、歉意、行动导向、具体 |
| 坏消息 | 坦诚 | 直接、有同理心、以解决方案为导向、尊重 |
| 问题/故障 | 紧迫 | 及时、透明、可执行、令人安心 |
| 升级 | 高管级 | 沉稳、主动承担、提出计划、有把握 |
| 计费/账户 | 精确 | 清晰、基于事实、有同理心、以解决为导向 |

### 按关系阶段调整语气

**新客户（0-3 个月）：**
- 更正式和专业
- 额外的背景和解释（不要假设对方了解太多）
- 主动提供帮助和资源
- 通过可靠性和及时响应建立信任

**老客户（3 个月以上）：**
- 温暖和合作
- 可以引用共同历史和过往对话
- 更直接高效的沟通
- 表现出对其目标和优先事项的了解

**沮丧或已升级的客户：**
- 额外的同理心和确认
- 回复及时
- 含具体承诺的具体行动计划
- 更短的反馈周期

### 写作风格规则

**应该：**
- 使用主动语态（"我们会调查"而非"此事将被调查"）
- 个人承诺用"我"，团队承诺用"我们"
- 分配行动时点名（"我们的工程团队 Sarah 将……"）
- 使用客户的术语，而非您的内部行话
- 提供具体日期和时间，而非相对表述（"1 月 24 日周五之前"而非"几天内"）
- 用标题或要点分解较长的回复

**不应该：**
- 使用企业行话或流行语
- 将责任推给其他团队、系统或流程
- 使用被动语态来回避责任（"错误已发生"）
- 加入不必要的注意事项或对冲语言
- 不必要地抄送他人——只包括需要参与的人
- 过度使用感叹号（每封邮件最多一个，如有必要）

## 特定情况处理方式

**回答产品问题：**
- 以直接答案开头
- 提供相关文档链接
- 如有需要，提议将他们对接到合适的资源
- 如果不知道答案：坦诚说明，承诺找到答案，给出时间线

**回应问题或 Bug：**
- 承认对其工作的影响
- 说明您所了解的问题及其状态
- 如有变通方案，提供之
- 设定解决时间线的预期
- 承诺按固定间隔提供更新

**处理升级：**
- 承认严重性和客户的沮丧
- 主动承担责任（不推卸或找借口）
- 提供含时间线的明确行动计划
- 指明负责解决的具体责任人
- 根据严重程度，提议电话或视频会议

**传达坏消息（功能停用、延期、无法修复）：**
- 直接说——不要掩埋主要信息
- 诚实地解释原因
- 具体承认对他们的影响
- 提供替代方案或缓解措施
- 提供明确的前进路径

**分享好消息（功能发布、里程碑、表彰）：**
- 以积极成果开头
- 将其与他们的具体目标或使用场景联系起来
- 建议利用好消息的下一步行动
- 表达真诚的热情

**拒绝请求（功能请求、折扣、例外情况）：**
- 承认请求及其理由
- 诚实地告知决定
- 在不显得轻蔑的情况下解释原因
- 尽可能提供替代方案
- 为未来对话留有余地

## 常见场景回复模板

### 确认 Bug 报告

```
Hi [Name],

Thank you for reporting this — I can see how [specific impact] would be
frustrating for your team.

I've confirmed the issue and escalated it to our engineering team as a
[priority level]. Here's what we know so far:
- [What's happening]
- [What's causing it, if known]
- [Workaround, if available]

I'll update you by [specific date/time] with a resolution timeline.
In the meantime, [workaround details if applicable].

Let me know if you have any questions or if this is impacting you in
other ways I should know about.

Best,
[Your name]
```

### 确认计费或账户问题

```
Hi [Name],

Thank you for reaching out about this — I understand billing issues
need prompt attention, and I want to make sure this gets resolved
quickly.

I've looked into your account and here's what I'm seeing:
- [What happened — clear factual explanation]
- [Impact on their account — charges, access, etc.]

Here's what I'm doing to fix this:
- [Action 1 — with timeline]
- [Action 2 — if applicable]

[If resolution is immediate: "This has been corrected and you should
see the change reflected within [timeframe]."]
[If needs investigation: "I'm escalating this to our billing team
and will have an update for you by [specific date]."]

I'm sorry for the inconvenience. Let me know if you have any
questions about your account.

Best,
[Your name]
```

### 拒绝不会构建的功能请求

```
Hi [Name],

Thank you for sharing this request — I can see why [capability] would
be valuable for [their use case].

I discussed this with our product team, and this isn't something we're
planning to build in the near term. The primary reason is [honest,
respectful explanation].

That said, I want to make sure you can accomplish your goal. Here are
some alternatives:
- [Alternative approach 1]
- [Alternative approach 2]
- [Integration or workaround if applicable]

I've also documented your request in our feedback system, and if our
direction changes, I'll let you know.

Would any of these alternatives work for your team? Happy to dig
deeper into any of them.

Best,
[Your name]
```

### 故障或事件沟通

```
Hi [Name],

I wanted to reach out directly to let you know about an issue affecting
[service/feature] that I know your team relies on.

**What happened:** [Clear, non-technical explanation]
**Impact:** [How it affects them specifically]
**Status:** [Current status — investigating / identified / fixing / resolved]
**ETA for resolution:** [Specific time if known, or "we'll update every X hours"]

[If applicable: "In the meantime, you can [workaround]."]

I'm personally tracking this and will update you as soon as we have a
resolution. You can also check [status page URL] for real-time updates.

I'm sorry for the disruption to your team's work. We take this seriously
and [what you're doing to prevent recurrence if known].

[Your name]
```

### 沉默后的跟进

```
Hi [Name],

I wanted to check in — I sent over [what you sent] on [date] and
wanted to make sure it didn't get lost in the shuffle.

[Brief reminder of what you need from them or what you're offering]

If now isn't a good time, no worries — just let me know when would be
better, and I'm happy to reconnect then.

Best,
[Your name]
```

## 跟进和升级指南

### 跟进节奏

| 情况 | 跟进时机 |
|------|---------|
| 未解答的问题 | 2-3 个工作日 |
| 进行中的支持问题 | 紧急问题每天跟进，标准问题 2-3 天 |
| 会后行动项 | 24 小时内（发送会议记录），然后在截止日期检查 |
| 一般跟进 | 根据进行中的问题按需 |
| 传达坏消息后 | 1 周后检查影响和情绪 |

### 何时升级

**升级给您的上级时：**
- 客户威胁取消或大幅减少合同
- 客户请求您无权批准的政策例外
- 问题未解决时间超过 SLA 允许
- 客户要求直接联系管理层
- 您犯了需要高级人员介入解决的错误

**升级给产品/工程时：**
- Bug 严重且阻塞客户业务
- 功能缺口导致竞争失利
- 客户有超出标准支持范围的特殊技术需求
- 集成问题需要工程调查

**升级格式：**
```
ESCALATION: [Customer Name] — [One-line summary]

Urgency: [Critical / High / Medium]
Customer impact: [What's broken for them]
History: [Brief background — 2-3 sentences]
What I've tried: [Actions taken so far]
What I need: [Specific help or decision needed]
Deadline: [When this needs to be resolved by]
```
