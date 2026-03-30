---
name: daily-briefing
description: 获取优先排序的每日销售简报。告知您的会议安排和优先事项即可独立使用，接入日历、CRM 和邮件后效果更强。触发词：日报简报、每日销售简报、daily briefing
---

# 每日销售简报

清晰了解今天最重要的事项。本技能可在您提供任何信息后立即运行，接入工具后效果更丰富。

## 工作原理

```
┌─────────────────────────────────────────────────────────────────┐
│                      DAILY BRIEFING                              │
├─────────────────────────────────────────────────────────────────┤
│  始终可用（独立运行）                                             │
│  ✓ 您提供：今日会议、关键交易、优先事项                           │
│  ✓ AI助手整理：当日优先行动计划                                   │
│  ✓ 输出：2 分钟可读完的简报                                      │
├─────────────────────────────────────────────────────────────────┤
│  增强效果（接入工具后）                                           │
│  + 日历：自动提取带与会者的今日会议                               │
│  + CRM：管道预警、任务、交易健康度                                │
│  + 邮件：关键账户的未读邮件、等待回复的邮件                       │
│  + 数据富化：账户的隔夜信号                                       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 快速开始

运行本技能时，AI助手会询问所需信息：

**如果未连接日历：**
> "您今天有哪些会议？（直接粘贴您的日历或列出来）"

**如果未连接 CRM：**
> "您本周重点关注哪些交易？有需要关注的交易吗？"

**如果已连接工具：**
AI助手会自动提取所有信息，直接展示简报。

---

## 连接器（可选）

连接您的工具来增强本技能：

| 连接器 | 新增功能 |
|--------|---------|
| **日历** | 带与会者、时间和背景的今日会议 |
| **CRM** | 在谈管道、即将关闭的交易、逾期任务、陈旧交易 |
| **邮件** | 来自商机联系人的未读邮件、等待回复的邮件 |
| **数据富化** | 隔夜信号：账户的融资、招聘、新闻动态 |

> **没有连接器？** 没关系。告诉AI助手您的会议和交易，AI助手会帮您创建简报。

---

## 输出格式

```markdown
# Daily Briefing | [Day, Month Date]

---

## #1 Priority

**[Most important thing to do today]**
[Why it matters and what to do about it]

---

## Today's Numbers

| Open Pipeline | Closing This Month | Meetings Today | Action Items |
|---------------|-------------------|----------------|--------------|
| $[X] | $[X] | [N] | [N] |

---

## Today's Meetings

### [Time] — [Company] ([Meeting Type])
**Attendees:** [Names]
**Context:** [One-line: deal status, last touch, what's at stake]
**Prep:** [Quick action before this meeting]

### [Time] — [Company] ([Meeting Type])
**Attendees:** [Names]
**Context:** [One-line context]
**Prep:** [Quick action]

*Run `call-prep [company]` for detailed meeting prep*

---

## Pipeline Alerts

### Needs Attention
| Deal | Stage | Amount | Alert | Action |
|------|-------|--------|-------|--------|
| [Deal] | [Stage] | $[X] | [Why flagged] | [What to do] |

### Closing This Week
| Deal | Close Date | Amount | Confidence | Blocker |
|------|------------|--------|------------|---------|
| [Deal] | [Date] | $[X] | [H/M/L] | [If any] |

---

## Email Priorities

### Needs Response
| From | Subject | Received |
|------|---------|----------|
| [Name @ Company] | [Subject] | [Time] |

### Waiting On Reply
| To | Subject | Sent | Days Waiting |
|----|---------|------|--------------|
| [Name @ Company] | [Subject] | [Date] | [N] |

---

## Suggested Actions

1. **[Action]** — [Why now]
2. **[Action]** — [Why now]
3. **[Action]** — [Why now]

---

*Run `call-prep [company]` before your meetings*
*Run `call-follow-up` after each call*
```

---

## 执行流程

### 第一步：收集背景

**如果已连接工具：**
```
1. 日历 → 获取今日事件
   - 筛选外部会议（有非公司内部与会者）
   - 提取：时间、标题、与会者、描述

2. CRM → 查询您的管道
   - 您负责的在谈商机
   - 标记：本周关闭、7天以上无活动、日期已滑动
   - 获取：逾期任务、即将到期任务

3. 邮件 → 检查优先消息
   - 来自商机联系人域名的未读邮件
   - 3天以上无回复的已发邮件

4. 数据富化 → 检查信号（如可用）
   - 在谈账户的融资、招聘、新闻动态
```

**如果没有连接器：**
```
询问用户：
1. "您今天有哪些会议？"
2. "您关注哪些交易？有即将关闭或需要关注的吗？"
3. "有什么紧急情况我需要了解吗？"

根据他们提供的信息工作。
```

### 第二步：优先排序

```
优先级排序：
1. 紧急：今天/明天即将关闭但尚未赢单的交易
2. 高优：今天与高价值商机的会议
3. 高优：来自决策者的未读邮件
4. 中优：本周内关闭的交易
5. 中优：陈旧交易（7天以上无活动）
6. 低优：本周到期的任务

选择 #1 优先事项：
- 如果今天有 >$50K 的交易会议 → 准备该会议
- 如果今天有交易要关闭 → 专注于成单
- 如果买家有紧急邮件 → 优先回复
- 否则 → 价值最高的陈旧交易
```

### 第三步：生成简报

```
根据可用数据组装各节内容：

1. #1 优先事项 — 始终包含（即使很简单）
2. 今日数字 — 如已连接 CRM，否则跳过
3. 今日会议 — 来自日历或用户输入
4. 管道预警 — 如已连接 CRM
5. 邮件优先级 — 如已连接邮件
6. 建议行动 — 始终包含前 3 个行动
```

---

## 快速模式

说 "quick brief" 或 "tldr my day" 获取缩略版本：

```markdown
# Quick Brief | [Date]

**#1:** [Priority action]

**Meetings:** [N] — [Company 1], [Company 2], [Company 3]

**Alerts:**
- [Alert 1]
- [Alert 2]

**Do Now:** [Single most important action]
```

---

## 收工模式

在最后一次会议后说 "wrap up my day" 或 "end of day summary"：

```markdown
# End of Day | [Date]

**Completed:**
- [Meeting 1] — [Outcome]
- [Meeting 2] — [Outcome]

**Pipeline Changes:**
- [Deal] moved to [Stage]

**Tomorrow's Focus:**
- [Priority 1]
- [Priority 2]

**Open Loops:**
- [ ] [Unfinished item needing follow-up]
```

---

## 使用建议

1. **首先连接日历** — 最大的节省时间利器
2. **其次连接 CRM** — 解锁管道预警
3. **即使没有连接器** — 只需告诉AI助手您的会议，AI助手会帮您排优先级

---

## 相关技能

- **call-prep** — 针对特定会议的深度准备
- **call-follow-up** — 通话后处理笔记
- **account-research** — 首次会面前调研公司
