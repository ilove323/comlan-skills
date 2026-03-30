---
name: call-summary
description: 处理通话笔记或文字稿——提取行动项、起草跟进邮件、生成内部摘要。适用于粘贴探索、演示或谈判通话后的粗略笔记或文字稿、起草客户跟进邮件、为 CRM 记录活动，或为团队捕捉异议和下一步。触发词：通话总结、会议记录、call summary
---

# /call-summary

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

处理通话笔记或文字稿，提取行动项、起草跟进沟通内容并更新相关记录。

## 用法

```
/call-summary <notes or transcript>
```

处理这些通话笔记：$ARGUMENTS

如果引用了文件：@$1

---

## 工作原理

```
┌─────────────────────────────────────────────────────────────────┐
│                      CALL SUMMARY                                │
├─────────────────────────────────────────────────────────────────┤
│  独立模式（始终可用）                                             │
│  ✓ 粘贴通话笔记或文字稿                                          │
│  ✓ 提取关键讨论点和决策内容                                      │
│  ✓ 识别带负责人和截止日期的行动项                                 │
│  ✓ 梳理异议、顾虑和待解问题                                      │
│  ✓ 起草面向客户的跟进邮件                                        │
│  ✓ 生成团队内部摘要                                              │
├─────────────────────────────────────────────────────────────────┤
│  增强效果（接入工具后）                                           │
│  + 转录：自动拉取录音（如 Gong、Fireflies）                       │
│  + CRM：更新商机、记录活动、创建任务                              │
│  + 邮件：直接从草稿发送跟进邮件                                   │
│  + 日历：关联会议、提取与会者背景                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 需要您提供的内容

**选项一：粘贴您的笔记**
粘贴任何您有的内容——要点、粗略笔记、意识流记录。AI助手会负责整理。

**选项二：粘贴文字稿**
如果您有来自视频会议工具（如 Zoom、Teams）或会话智能工具（如 Gong、Fireflies）的完整文字稿，直接粘贴即可。AI助手会提取关键时刻。

**选项三：描述通话情况**
告诉AI助手发生了什么："与 Acme Corp 进行了一次探索通话。与他们的 VP 工程和 CTO 开会。他们在评估我们和竞争对手 X。主要顾虑是集成时间线。"

---

## 输出内容

### 内部摘要
```markdown
## Call Summary: [Company] — [Date]

**Attendees:** [Names and titles]
**Call Type:** [Discovery / Demo / Negotiation / Check-in]
**Duration:** [If known]

### Key Discussion Points
1. [Topic] — [What was discussed, decisions made]
2. [Topic] — [Summary]

### Customer Priorities
- [Priority 1 they expressed]
- [Priority 2]

### Objections / Concerns Raised
- [Concern] — [How you addressed it / status]

### Competitive Intel
- [Any competitor mentions, what was said]

### Action Items
| Owner | Action | Due |
|-------|--------|-----|
| [You] | [Task] | [Date] |
| [Customer] | [Task] | [Date] |

### Next Steps
- [Agreed next step with timeline]

### Deal Impact
- [How this call affects the opportunity — stage change, risk, acceleration]
```

### 面向客户的跟进邮件
```
Subject: [Meeting recap + next steps]

Hi [Name],

Thank you for taking the time to meet today...

[Key points discussed]

[Commitments you made]

[Clear next step with timeline]

Best,
[You]
```

---

## 邮件风格指南

起草面向客户的邮件时：

1. **简洁但信息充分** — 直奔主题。客户很忙。
2. **不使用 Markdown 格式** — 不要用星号、加粗或其他 Markdown 语法。用在任何邮件客户端都看起来自然的纯文本。
3. **使用简单结构** — 短段落，各节之间换行。除非客户的邮件客户端能渲染，否则不用标题或列表格式。
4. **保持可快速扫读** — 如果列举条目，使用普通破折号或数字，不用花哨格式。

**正确示例：**
```
Here's what we discussed:
- Quote for 20 seats at $480/seat/year
- W9 and supplier onboarding docs
- Point of contact for the contract
```

**错误示例：**
```
**What You Need from Us:**
- Quote for 20 seats at $480/seat/year
```

---

## 如果已连接工具

**已连接转录工具（如 Gong、Fireflies）：**
- AI助手会自动搜索该通话
- 提取完整文字稿
- 提取平台标记的关键时刻

**已连接 CRM：**
- AI助手会提议更新商机阶段
- 将通话记录为活动
- 为行动项创建任务
- 更新下一步字段

**已连接邮件：**
- AI助手会提议在 ~~email 中创建草稿
- 或在您批准后直接发送

---

## 使用建议

1. **内容越详细，输出越好** — 即使粗略的笔记也有帮助。"他们似乎对 X 有顾虑" 也是有用的背景信息。
2. **提供与会者姓名** — 帮助AI助手整理摘要并分配行动项。
3. **标明重要内容** — 如果某件事很重要，请告知："重点是……"
4. **告知交易阶段** — 帮助AI助手调整跟进邮件的语气和下一步建议。
