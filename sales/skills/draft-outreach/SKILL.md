---
name: draft-outreach
description: 先调研潜在客户，再起草个性化外联邮件。默认使用网络调研，接入数据富化工具和 CRM 后效果更强。触发词：起草外联、写开发信、draft outreach
---

# 起草外联

先调研，后起草。本技能从不发送通用外联——它总是先调研潜在客户，再个性化消息内容。默认通过网络搜索独立运行，接入工具后效果显著提升。

## 连接器（可选）

| 连接器 | 新增功能 |
|--------|---------|
| **数据富化** | 已验证邮件、电话、详细背景信息 |
| **CRM** | 既往关系背景、现有联系人 |
| **邮件** | 直接在收件箱中创建草稿 |

> **没有连接器？** 网络调研效果很好。AI助手会输出邮件文本供您复制。

---

## 工作原理

```
+------------------------------------------------------------------+
|                      DRAFT OUTREACH                               |
|                                                                   |
|  Step 1: RESEARCH (always happens first)                         |
|  - Web search (default)                                           |
|  - + Enrichment (if enrichment tools connected)                  |
|  - + CRM (if CRM connected)                                      |
|                                                                   |
|  Step 2: DRAFT (based on research)                               |
|  - Personalized opening (from research)                          |
|  - Relevant hook (their priorities)                              |
|  - Clear CTA                                                      |
|                                                                   |
|  Step 3: DELIVER (based on connectors)                           |
|  - Email draft (if email connected)                              |
|  - Copy for LinkedIn (always)                                    |
|  - Output to user (always)                                        |
+------------------------------------------------------------------+
```

---

## 输出格式

```markdown
# Outreach Draft: [Person] @ [Company]
**Generated:** [Date] | **Research Sources:** [Web, Enrichment, CRM]

---

## Research Summary

**Target:** [Name], [Title] at [Company]
**Hook:** [Why reaching out now - the personalized angle]
**Goal:** [What you want from this outreach]

---

## Email Draft

**To:** [email if known, or "find email" note]
**Subject:** [Personalized subject line]

---

[Email body]

---

**Subject Line Alternatives:**
1. [Option 2]
2. [Option 3]

---

## LinkedIn Message (if no email)

**Connection Request (< 300 chars):**
[Short, no-pitch connection request]

**Follow-up Message (after connected):**
[Value-first message]

---

## Why This Approach

| Element | Based On |
|---------|----------|
| Opening | [Research finding that makes it personal] |
| Hook | [Their priority/pain point] |
| Proof | [Relevant customer story] |
| CTA | [Low-friction ask] |

---

## Email Draft Status

[Draft created - check ~~email]
[Email not connected - copy email above]
[No email found - use LinkedIn approach]

---

## Follow-up Sequence (Optional)

**Day 3 - Follow-up 1:**
[Short, new angle]

**Day 7 - Follow-up 2:**
[Different value prop]

**Day 14 - Break-up:**
[Final attempt]
```

---

## 执行流程

### 第一步：解析请求

```
输入模式：
- "draft outreach to John Smith at Acme" → 人员 + 公司
- "write cold email to Acme's CTO" → 职位 + 公司
- "reach out to sarah@acme.com" → 已提供邮件
- "LinkedIn message to [LinkedIn URL]" → 已提供主页
```

### 第二步：先调研（始终执行）

**内部调用 research-prospect 技能：**
```
1. 搜索公司 + 人员
2. 如已连接数据富化：获取已验证联系方式和背景信息
3. 如已连接 CRM：检查既往关系
```

**起草前必须找到：**
- 此人身份（职位、背景）
- 公司业务
- 近期新闻或触发事件
- 个性化切入点

### 第三步：识别切入点

```
切入点优先级：
1. 触发事件（融资、招聘、新闻）→ 最及时
2. 共同联系人 → 社会证明
3. 对方的内容（帖子、文章、演讲）→ 展示您做了功课
4. 公司举措 → 与其优先事项相关
5. 基于职位的痛点 → 最不个性化但仍有价值
```

### 第四步：起草消息

**邮件结构（AIDA）：**
```
SUBJECT: [个性化，< 50 字符，无垃圾邮件词汇]

[开场：个人切入点——展示您调研了对方]

[兴趣：用 1-2 句话描述其问题/机会]

[欲望：简短的证明点——类似公司的结果]

[行动：明确、低摩擦的 CTA]

[签名]
```

**LinkedIn 联系请求（< 300 字符）：**
```
Hi [Name], [共同联系/共同兴趣/真诚赞美].
Would love to connect. [无推销]
```

**LinkedIn 跟进消息：**
```
Thanks for connecting! [价值优先：洞察、文章、观察]

[自然过渡到联系原因]

[提问，而非推销]
```

### 第五步：创建邮件草稿

```
如果已连接邮件：
1. 创建含收件人、主题、正文的草稿
2. 返回草稿链接
3. 备注："草稿已创建——审核后发送"

如果未连接：
1. 输出邮件文本
2. 备注："复制到您的邮件客户端"
```

---

## 按连接器划分的能力

| 能力 | 仅网络 | + 数据富化 | + CRM | + 邮件 |
|------|--------|-----------|-------|-------|
| 个性化开场 | 基础 | 深度 | 含历史 | 相同 |
| 已验证邮件 | 否 | 是 | 是 | 是 |
| 详细背景 | 仅公开 | 完整 | 完整 | 完整 |
| 既往关系 | 否 | 否 | 是 | 是 |
| 自动创建草稿 | 否 | 否 | 否 | 是 |

---

## 场景消息模板

### 冷启动外联（无既往关系）

```
Subject: [Their initiative] + [your angle]

Hi [Name],

[Personal hook based on research - news, content, mutual connection].

[1 sentence on their likely challenge based on role/company].

[Brief proof: "We helped [Similar Company] achieve [Result]".]

Worth a 15-min call to see if relevant?

[Signature]
```

### 温启动外联（曾见面/有共同联系人）

```
Subject: Following up from [context]

Hi [Name],

[Reference to how you know them / who connected you].

[Why reaching out now - their trigger].

[Specific value you can offer].

[CTA]
```

### 重新激活（联系中断）

```
Subject: [Short, curiosity-driven]

Hi [Name],

[Acknowledge time passed without being guilt-trippy].

[New reason to reconnect - their news or your news].

[Simple question to re-open dialogue].

[Signature]
```

### 活动后跟进

```
Subject: Great meeting you at [Event]

Hi [Name],

[Specific memory from conversation].

[Value-add: article, intro, resource related to what you discussed].

[Soft CTA for next conversation].
```

---

## 邮件风格指南

1. **简洁但信息充分** — 直奔主题。忙碌的人会快速浏览。
2. **不使用 Markdown 格式** — 绝不使用星号、加粗（**text**）或其他 Markdown。写在任何邮件客户端都看起来自然的纯文本。
3. **短段落** — 每段最多 2-3 句话。留白是您的朋友。
4. **简单列表** — 列举条目时，使用普通破折号，不用花哨格式。

**正确示例：**
```
Here's what I can share:
- Case study from a similar company
- 15-min intro call this week
- Quick demo if helpful
```

**错误示例：**
```
**What I Can Offer:**
- **Case study** from a similar company
- **Intro call** this week
```

---

## 避免的写法

**通用开场：**
- "I hope this email finds you well"
- "I'm reaching out because..."
- "I wanted to introduce myself"

**功能堆砌：**
- 长段关于您产品的介绍
- 一次提多个价值主张
- 没有明确的 CTA

**假个性化：**
- "I noticed you work at [Company]"（显然的废话）
- "Congrats on your role"（没有背景信息的祝贺）

**邮件中的 Markdown：**
- 使用 **加粗** 或 *斜体* 星号
- 无法渲染的标题或格式化列表

**正确做法：**
- 以您学到的具体内容开场
- 一个清晰的价值主张
- 一个清晰的诉求
- 仅使用纯文本格式

---

## 渠道选择

```
如果有已验证邮件：
  → 优先使用邮件（回复率更高）
  → 同时提供 LinkedIn 备选方案

如果没有邮件：
  → LinkedIn 联系请求
  → 连接后的跟进消息模板

如果有温介绍机会：
  → 建议先通过共同联系人介绍
```

---

## 公司配置【可自定义】

```markdown
## Outreach Settings

- My name: [Your Name]
- My title: [Your Title]
- My company: [Company Name]
- Value prop: [One sentence - what you help with]

## Signature
[Your preferred email signature]

## Proof Points
- [Customer 1]: [Result]
- [Customer 2]: [Result]
- [Customer 3]: [Result]

## CTA Options
- Default: "Worth a 15-min call?"
- Softer: "Open to learning more?"
- Specific: "Can I send over a quick demo?"

## Tone
- [Professional / Casual / Direct]
- Industry-specific language: [Yes/No]
```

---

## 示例

**输入：** "draft outreach to the Head of Engineering at Notion"

**调研发现：**
- 姓名：David Tibbitts
- 近期新闻：Notion 推出了 AI 功能
- 招聘：5 个 ML 工程师职位在招
- 背景：前 Facebook，Stanford CS

**输出：**

```markdown
# Outreach Draft: David Tibbitts @ Notion

## Research Summary
**Target:** David Tibbitts, Head of Engineering at Notion
**Hook:** They're scaling AI features and hiring ML talent
**Goal:** Intro call about AI infrastructure

---

## Email Draft

**To:** david@notion.so
**Subject:** Notion's AI scaling + a thought

---

Hi David,

Saw Notion's AI rollout is gaining serious traction - congrats.
With 5 ML roles open, seems like you're scaling fast.

Curious how you're thinking about inference infrastructure
as usage grows. We helped [Similar Company] cut their AI
serving costs 40% while improving latency.

Worth a 15-min call to see if relevant to your roadmap?

Best,
[Name]

---

**Subject Alternatives:**
1. Notion AI + scaling question
2. Quick thought on Notion's ML hiring

---

## Email Draft Status
Draft created - check ~~email
```
