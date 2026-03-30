---
name: call-prep
description: 为销售通话准备账户背景、与会者调研及建议议程。默认通过用户输入和网络调研独立运行，接入 CRM、邮件、即时通讯或转录工具后效果更强。触发词：通话准备、销售拜访准备、call prep
---

# 通话准备

在数分钟内充分备战任何销售通话。本技能可在您提供任意背景信息后立即运行，接入销售工具后效果显著提升。

## 工作原理

```
┌─────────────────────────────────────────────────────────────────┐
│                        CALL PREP                                 │
├─────────────────────────────────────────────────────────────────┤
│  始终可用（独立运行）                                             │
│  ✓ 您提供：公司、会议类型、与会者                                 │
│  ✓ 网络搜索：近期新闻、融资情况、管理层变动                       │
│  ✓ 公司调研：业务、规模、行业                                     │
│  ✓ 输出：含议程和问题的准备简报                                   │
├─────────────────────────────────────────────────────────────────┤
│  增强效果（接入工具后）                                           │
│  + CRM：账户历史、联系人、商机、活动记录                          │
│  + 邮件：近期往来邮件、待解问题、已作承诺                         │
│  + 即时通讯：内部讨论、同事见解                                   │
│  + 转录：过往通话录音、关键时刻                                   │
│  + 日历：自动找到会议并提取与会者                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 快速开始

运行本技能时，AI助手会询问所需信息：

**必填项：**
- 公司或联系人姓名
- 会议类型（探索、演示、谈判、跟进等）

**有则更好：**
- 与会者信息（姓名与职位）
- 您希望AI助手了解的任何背景（可粘贴过往笔记、邮件等）

如果已连接 CRM、邮件或其他工具，AI助手会自动拉取背景信息并跳过提问。

---

## 连接器（可选）

连接您的工具来增强本技能：

| 连接器 | 新增功能 |
|--------|---------|
| **CRM** | 账户详情、联系人历史、在谈交易、近期活动 |
| **邮件** | 与该公司的近期往来邮件、待解问题、已分享附件 |
| **即时通讯** | 关于该账户的内部讨论（如 Slack）、同事见解 |
| **转录** | 过往通话录音、已涉及话题、竞品提及 |
| **日历** | 自动找到会议、提取与会者和会议描述 |

> **没有连接器？** 没关系。只需告诉AI助手会议情况并粘贴您拥有的任何背景，AI助手会负责调研其余内容。

---

## 输出格式

```markdown
# Call Prep: [Company Name]

**Meeting:** [Type] — [Date/Time if known]
**Attendees:** [Names with titles]
**Your Goal:** [What you want to accomplish]

---

## Account Snapshot

| Field | Value |
|-------|-------|
| **Company** | [Name] |
| **Industry** | [Industry] |
| **Size** | [Employees / Revenue if known] |
| **Status** | [New prospect / Active opportunity / Customer] |
| **Last Touch** | [Date and summary] |

---

## Who You're Meeting

### [Name] — [Title]
- **Background:** [Career history, education if found]
- **LinkedIn:** [URL]
- **Role in Deal:** [Decision maker / Champion / Evaluator / etc.]
- **Last Interaction:** [Summary if known]
- **Talking Point:** [Something personal/professional to reference]

[Repeat for each attendee]

---

## Context & History

**What's happened so far:**
- [Key point from prior interactions]
- [Open commitments or action items]
- [Any concerns or objections raised]

**Recent news about [Company]:**
- [News item 1 — why it matters]
- [News item 2 — why it matters]

---

## Suggested Agenda

1. **Open** — [Reference last conversation or trigger event]
2. **[Topic 1]** — [Discovery question or value discussion]
3. **[Topic 2]** — [Address known concern or explore priority]
4. **[Topic 3]** — [Demo section / Proposal review / etc.]
5. **Next Steps** — [Propose clear follow-up with timeline]

---

## Discovery Questions

Ask these to fill gaps in your understanding:

1. [Question about their current situation]
2. [Question about pain points or priorities]
3. [Question about decision process and timeline]
4. [Question about success criteria]
5. [Question about other stakeholders]

---

## Potential Objections

| Objection | Suggested Response |
|-----------|-------------------|
| [Likely objection based on context] | [How to address it] |
| [Common objection for this stage] | [How to address it] |

---

## Internal Notes

[Any internal chat context (e.g. Slack), colleague insights, or competitive intel]

---

## After the Call

Run **call-follow-up** to:
- Extract action items
- Update your CRM
- Draft follow-up email
```

---

## 执行流程

### 第一步：收集背景

**如果已连接工具：**
```
1. 日历 → 根据公司名称找到即将到来的会议
   - 提取：标题、时间、与会者、描述、附件

2. CRM → 查询账户
   - 提取：账户详情、所有联系人、在谈商机
   - 提取：最近 10 条活动、任何账户备注

3. 邮件 → 搜索近期往来邮件
   - 查询：过去 30 天内与该公司域名的邮件往来
   - 提取：关键话题、待解问题、已作承诺

4. 即时通讯 → 搜索内部讨论
   - 查询：过去 30 天内的公司名称提及
   - 提取：同事见解、竞情

5. 转录 → 找到过往通话
   - 提取：与该账户的通话录音
   - 提取：关键时刻、已提出的异议、已涉及话题
```

**如果没有连接器：**
```
1. 询问用户：
   - "您要与哪家公司开会？"
   - "这次是什么类型的会议？"
   - "谁将参会？（姓名和职位，如果知道）"
   - "有什么背景信息希望我了解吗？（可粘贴笔记、邮件等）"

2. 接受用户提供的任何信息并据此工作
```

### 第二步：网络补充调研

**始终执行（网络搜索）：**
```
1. "[Company] news" — 过去 30 天
2. "[Company] funding" — 近期公告
3. "[Company] leadership" — 高管变动
4. "[Company] + [industry] trends" — 相关背景
5. 与会者 LinkedIn 主页 — 背景调研
```

### 第三步：综合与生成

```
1. 将所有来源信息整合为统一背景
2. 找出理解盲区 → 生成探索性问题
3. 根据阶段和历史预判可能的异议
4. 根据会议类型制定建议议程
5. 输出格式化的准备简报
```

---

## 会议类型变体

### 探索通话
- 重点：了解客户现状、痛点、优先事项
- 议程侧重：多问少说
- 关键输出：资质信号、下一步提案

### 演示/汇报
- 重点：客户的具体使用场景、针对性示例
- 议程侧重：展示相关功能、收集反馈
- 关键输出：技术要求、决策时间线

### 谈判/方案评审
- 重点：解决顾虑、证明价值
- 议程侧重：处理异议、弥合分歧
- 关键输出：达成协议的路径、明确的下一步

### 跟进/季度业务回顾
- 重点：已交付价值、扩展机会
- 议程侧重：回顾成果、发现新需求
- 关键输出：续约信心、追加销售管道

---

## 获得更好准备效果的建议

1. **背景越多，准备越好** — 粘贴邮件、笔记或任何您有的信息
2. **提供与会者信息** — 即使只是职位也有帮助
3. **说明您的目标** — "我想让他们同意进行试点"
4. **标明顾虑** — "他们提到预算紧张"

---

## 相关技能

- **account-research** — 首次联系前对公司进行深度调研
- **call-follow-up** — 处理通话笔记并执行通话后工作流
- **draft-outreach** — 调研后撰写个性化外联邮件
