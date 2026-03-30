---
name: kb-article
description: 根据已解决问题或常见问题起草知识库文章。适用于工单解决方案值得记录以便自助服务、同一问题反复出现、需要发布变通方案，或应将已知问题告知客户等情况。触发词：知识库文章、写FAQ、kb article
---

# /kb-article

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

根据已解决的支持问题、常见问题或已记录的变通方案，起草可直接发布的知识库文章。内容经过结构化处理，便于搜索和自助服务。

## 用法

```
/kb-article <resolved issue, ticket reference, or topic description>
```

示例：
- `/kb-article How to configure SSO with Okta — resolved this for 3 customers last month`
- `/kb-article Ticket #4521 — customer couldn't export data over 10k rows`
- `/kb-article Common question: how to set up webhook notifications`
- `/kb-article Known issue: dashboard charts not loading on Safari 16`

## 工作流程

### 1. 理解来源材料

解析输入，识别：

- **问题是什么？** 原始问题、报错或错误
- **解决方案是什么？** 解决办法、变通方案或答案
- **影响谁？** 用户类型、套餐级别或配置
- **出现频率如何？** 一次性还是反复发生的问题
- **最适合哪种文章类型？** 操作指南、故障排查、FAQ、已知问题或参考文档（见下方文章类型）

如果提供了工单编号，查找完整背景：

- **~~support platform**：提取工单线程、解决方案和任何内部备注
- **~~knowledge base**：检查是否存在类似文章（更新 vs. 创建新文章）
- **~~project tracker**：检查是否有相关 Bug 或功能请求

### 2. 起草文章

使用以下文章结构、格式规范和搜索优化最佳实践：

- 按照所选文章类型的模板（操作指南、故障排查、FAQ、已知问题或参考文档）
- 应用搜索优化最佳实践：客户语言标题、直白语言的开头句、精确的错误信息、常见同义词
- 保持可扫读：标题、编号步骤、短段落

### 3. 生成文章

附上元数据展示草稿：

```
## KB Article Draft

**Title:** [Article title]
**Type:** [How-to / Troubleshooting / FAQ / Known Issue / Reference]
**Category:** [Product area or topic]
**Tags:** [Searchable tags]
**Audience:** [All users / Admins / Developers / Specific plan]

---

[Full article content — using the appropriate template below]

---

### Publishing Notes
- **Source:** [Ticket #, customer conversation, or internal discussion]
- **Existing articles to update:** [If this overlaps with existing content]
- **Review needed from:** [SME or team if technical accuracy needs verification]
- **Suggested review date:** [When to revisit for accuracy]
```

### 4. 提供后续步骤

生成文章后：
- "需要我检查您的 ~~knowledge base 中是否已有类似文章？"
- "需要我调整技术深度以适应不同受众吗？"
- "需要我起草配套文章（例如，在此故障排查指南的基础上添加操作指南）吗？"
- "需要我创建一个仅供内部使用的版本，包含额外的技术细节吗？"

---

## 文章结构和格式规范

### 通用文章元素

每篇知识库文章都应包含：

1. **标题**：清晰、可搜索，描述结果或问题（非内部行话）
2. **概述**：1-2 句话说明文章涵盖的内容及适用对象
3. **正文**：适合文章类型的结构化内容
4. **相关文章**：相关配套内容的链接
5. **元数据**：类别、标签、受众、最后更新日期

### 格式规则

- **使用标题（H2、H3）** 将内容分成可扫读的节
- **使用编号列表** 处理顺序步骤
- **使用要点列表** 处理非顺序条目
- **使用加粗** 标注界面元素名称、关键术语和重点内容
- **使用代码块** 处理命令、API 调用、错误信息和配置值
- **使用表格** 处理对比、选项或参考数据
- **使用标注/备注** 处理警告、技巧和重要注意事项
- **保持段落简短** — 每段最多 2-4 句话
- **每节一个主题** — 如果一节涵盖两个话题，拆分它

## 面向搜索优化的写作

文章如果客户找不到就没有意义。对每篇文章进行搜索优化：

### 标题最佳实践

| 好标题 | 差标题 | 原因 |
|--------|--------|------|
| "How to configure SSO with Okta" | "SSO Setup" | 具体，包含客户搜索的工具名称 |
| "Fix: Dashboard shows blank page" | "Dashboard Issue" | 包含客户遇到的症状 |
| "API rate limits and quotas" | "API Information" | 包含客户搜索的具体术语 |
| "Error: 'Connection refused' when importing data" | "Import Problems" | 包含精确的错误信息 |

### 关键词优化

- **包含精确错误信息** — 客户会将错误文本复制粘贴到搜索框
- **使用客户语言**，而非内部术语 — "can't log in"而非"authentication failure"
- **包含常见同义词** — "delete/remove"、"dashboard/home page"、"export/download"
- **添加替代表述** — 在概述中从不同角度说明同一问题
- **按产品区域打标签** — 确保类别和标签与客户对产品的思维方式一致

### 开头句公式

每篇文章以一句话开头，用通俗语言重新陈述问题或任务：

- **操作指南**："This guide shows you how to [accomplish X]."
- **故障排查**："If you're seeing [symptom], this article explains how to fix it."
- **FAQ**："[Question in the customer's words]? Here's the answer."
- **已知问题**："Some users are experiencing [symptom]. Here's what we know and how to work around it."

## 文章类型模板

### 操作指南

**用途**：完成任务的分步操作说明。

**结构**：
```
# How to [accomplish task]

[Overview — what this guide covers and when you'd use it]

## Prerequisites
- [What's needed before starting]

## Steps
### 1. [Action]
[Instruction with specific details]

### 2. [Action]
[Instruction]

## Verify It Worked
[How to confirm success]

## Common Issues
- [Issue]: [Fix]

## Related Articles
- [Links]
```

**最佳实践**：
- 每个步骤以动词开头
- 包含具体路径："Go to Settings > Integrations > API Keys"
- 说明每步后用户应该看到什么（"您应该看到绿色确认横幅"）
- 亲自测试步骤或用近期工单解决方案验证

### 故障排查文章

**用途**：诊断和解决特定问题。

**结构**：
```
# [Problem description — what the user sees]

## Symptoms
- [What the user observes]

## Cause
[Why this happens — brief, non-jargon explanation]

## Solution
### Option 1: [Primary fix]
[Steps]

### Option 2: [Alternative if Option 1 doesn't work]
[Steps]

## Prevention
[How to avoid this in the future]

## Still Having Issues?
[How to get help]
```

**最佳实践**：
- 以症状开头而非原因——客户搜索的是他们看到的现象
- 尽可能提供多种解决方案（最可能的修复方案放第一）
- 包含"仍有问题？"节，指向支持
- 如果根本原因复杂，对客户的解释保持简洁

### FAQ 文章

**用途**：常见问题的快速解答。

**结构**：
```
# [Question — in the customer's words]

[Direct answer — 1-3 sentences]

## Details
[Additional context, nuance, or explanation if needed]

## Related Questions
- [Link to related FAQ]
- [Link to related FAQ]
```

**最佳实践**：
- 在第一句话中回答问题
- 保持简洁——如果答案需要演示步骤，它是操作指南而非 FAQ
- 将相关 FAQ 分组并相互链接

### 已知问题文章

**用途**：记录有变通方案的已知 Bug 或限制。

**结构**：
```
# [Known Issue]: [Brief description]

**Status:** [Investigating / Workaround Available / Fix In Progress / Resolved]
**Affected:** [Who/what is affected]
**Last updated:** [Date]

## Symptoms
[What users experience]

## Workaround
[Steps to work around the issue, or "No workaround available"]

## Fix Timeline
[Expected fix date or current status]

## Updates
- [Date]: [Update]
```

**最佳实践**：
- 保持状态最新——过时的已知问题文章比没有文章更损害信任
- 修复上线时更新文章并标记为已解决
- 如已解决，文章继续保留 30 天供仍在搜索旧症状的客户使用

## 审阅和维护周期

没有维护的知识库会衰退。遵循以下时间表：

| 活动 | 频率 | 责任人 |
|------|------|--------|
| **新文章审阅** | 发布前 | 同行审阅 + 技术内容 SME 审阅 |
| **准确性审计** | 每季度 | 支持团队审阅高流量文章 |
| **陈旧内容检查** | 每月 | 标记超过 6 个月未更新的文章 |
| **已知问题更新** | 每周 | 更新所有开放已知问题的状态 |
| **数据分析审阅** | 每月 | 检查帮助度评分低或跳出率高的文章 |
| **缺口分析** | 每季度 | 找出没有知识库文章的高频工单话题 |

### 文章生命周期

1. **草稿**：已写就，待审阅
2. **已发布**：上线并对客户可见
3. **需更新**：已标记需要修订（产品变更、反馈或时效）
4. **已归档**：不再相关但保留作参考
5. **已退役**：从知识库中移除

### 何时更新 vs. 创建新文章

**更新现有文章**的情况：
- 产品发生变化，步骤需要更新
- 文章基本正确但缺少某个细节
- 反馈表明客户对某个特定节感到困惑
- 找到了更好的变通方案或解决方案

**创建新文章**的情况：
- 新功能或产品区域需要文档
- 已解决的工单揭示了一个缺口——该话题没有现有文章
- 现有文章涵盖了太多话题，应该拆分
- 不同受众需要对同一信息的不同解释

## 链接和分类法

### 类别结构

按照客户思维方式组织文章：

```
Getting Started
├── Account setup
├── First-time configuration
└── Quick start guides

Features & How-tos
├── [Feature area 1]
├── [Feature area 2]
└── [Feature area 3]

Integrations
├── [Integration 1]
├── [Integration 2]
└── API reference

Troubleshooting
├── Common errors
├── Performance issues
└── Known issues

Billing & Account
├── Plans and pricing
├── Billing questions
└── Account management
```

### 链接最佳实践

- **从故障排查链接到操作指南**："如需设置说明，请参阅 [How to configure X]"
- **从操作指南链接到故障排查**："如遇到错误，请参阅 [Troubleshooting X]"
- **从 FAQ 链接到详细文章**："如需完整演示，请参阅 [Guide to X]"
- **从已知问题链接到变通方案**：保持从问题到解决方案的链路简短
- **在知识库内使用相对链接** — 它们在重构时比绝对 URL 更耐用
- **避免循环链接** — 如果 A 链接到 B，B 不应再链接回 A，除非两者都是真正有用的入口

## 知识库写作最佳实践

1. 为沮丧且在搜索答案的客户写作——清晰、直接、有帮助
2. 每篇文章都应该通过搜索找到，使用客户会输入的词汇
3. 测试您的文章——自己按步骤操作，或让不熟悉该话题的人来操作
4. 保持文章聚焦——一个问题，一个解决方案。如果文章越来越长，拆分它
5. 积极维护——错误的文章比没有文章更糟糕
6. 跟踪缺口——每个本可以是知识库文章的工单都是内容缺口
7. 衡量影响——没有流量或不能减少工单的文章需要改进或退役
