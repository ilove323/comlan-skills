---
name: pipeline-review
description: 分析管道健康状况——排序交易优先级、标记风险、获取每周行动计划。适用于每周管道复盘、决定本周重点关注哪些交易、发现陈旧或停滞的商机、检查关闭日期不合理等数据卫生问题，或识别单线程联系的交易。触发词：管道复盘、机会管理、pipeline review
---

# /pipeline-review

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

分析您的管道健康状况，排序交易优先级，获取可落地的建议来指引您的工作重心。

## 用法

```
/pipeline-review [segment or rep]
```

复盘管道（对象）：$ARGUMENTS

如果引用了文件：@$1

---

## 工作原理

```
┌─────────────────────────────────────────────────────────────────┐
│                     PIPELINE REVIEW                              │
├─────────────────────────────────────────────────────────────────┤
│  独立模式（始终可用）                                             │
│  ✓ 上传 CRM 导出的 CSV                                          │
│  ✓ 或粘贴/描述您的交易                                           │
│  ✓ 健康检查：标记陈旧、停滞和高风险交易                           │
│  ✓ 优先排序：按影响力和可关闭性排列交易                           │
│  ✓ 数据卫生审计：缺失数据、不合理关闭日期、单线程联系             │
│  ✓ 每周行动计划：明确工作重心                                     │
├─────────────────────────────────────────────────────────────────┤
│  增强效果（接入工具后）                                           │
│  + CRM：自动拉取管道，更新记录                                    │
│  + 参与度评分的活动数据                                           │
│  + 风险预测的历史规律                                             │
│  + 日历：查看每个交易的即将会议                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 需要您提供的内容

**选项 A：上传 CSV**
从 CRM（如 Salesforce、HubSpot）导出您的管道。有用的字段包括：
- 交易/商机名称
- 账户名称
- 金额
- 阶段
- 关闭日期
- 创建日期
- 最后活动日期
- 负责人（复盘团队时）
- 主要联系人

**选项 B：粘贴您的交易**
```
Acme Corp - $50K - Negotiation - closes Jan 31 - last activity Jan 20
TechStart - $25K - Demo scheduled - closes Feb 15 - no activity in 3 weeks
BigCo - $100K - Discovery - closes Mar 30 - created last week
```

**选项 C：描述您的管道**
"我有 12 个交易。两个大的在谈判，我有把握。三个在探索阶段卡了一个多月。其余处于中间阶段，但有些我已经很久没联系了。"

---

## 输出内容

```markdown
# Pipeline Review: [Date]

**Data Source:** [CSV upload / Manual input / CRM]
**Deals Analyzed:** [X]
**Total Pipeline Value:** $[X]

---

## Pipeline Health Score: [X/100]

| Dimension | Score | Issue |
|-----------|-------|-------|
| **Stage Progression** | [X]/25 | [X] deals stuck in same stage 30+ days |
| **Activity Recency** | [X]/25 | [X] deals with no activity in 14+ days |
| **Close Date Accuracy** | [X]/25 | [X] deals with close date in past |
| **Contact Coverage** | [X]/25 | [X] deals single-threaded |

---

## Priority Actions This Week

### 1. [Highest Priority Deal]
**Why:** [Reason — large, closing soon, at risk, etc.]
**Action:** [Specific next step]
**Impact:** $[X] if you close it

### 2. [Second Priority]
**Why:** [Reason]
**Action:** [Next step]

### 3. [Third Priority]
**Why:** [Reason]
**Action:** [Next step]

---

## Deal Prioritization Matrix

### Close This Week (Focus Time Here)
| Deal | Amount | Stage | Close Date | Next Action |
|------|--------|-------|------------|-------------|
| [Deal] | $[X] | [Stage] | [Date] | [Action] |

### Close This Month (Keep Warm)
| Deal | Amount | Stage | Close Date | Status |
|------|--------|-------|------------|--------|
| [Deal] | $[X] | [Stage] | [Date] | [Status] |

### Nurture (Check-in Periodically)
| Deal | Amount | Stage | Close Date | Status |
|------|--------|-------|------------|--------|
| [Deal] | $[X] | [Stage] | [Date] | [Status] |

---

## Risk Flags

### Stale Deals (No Activity 14+ Days)
| Deal | Amount | Last Activity | Days Silent | Recommendation |
|------|--------|---------------|-------------|----------------|
| [Deal] | $[X] | [Date] | [X] | [Re-engage / Downgrade / Remove] |

### Stuck Deals (Same Stage 30+ Days)
| Deal | Amount | Stage | Days in Stage | Recommendation |
|------|--------|-------|---------------|----------------|
| [Deal] | $[X] | [Stage] | [X] | [Push / Multi-thread / Qualify out] |

### Past Close Date
| Deal | Amount | Close Date | Days Overdue | Recommendation |
|------|--------|------------|--------------|----------------|
| [Deal] | $[X] | [Date] | [X] | [Update date / Push to next quarter / Close lost] |

### Single-Threaded (Only One Contact)
| Deal | Amount | Contact | Risk | Recommendation |
|------|--------|---------|------|----------------|
| [Deal] | $[X] | [Name] | Champion leaves = deal dies | [Identify additional stakeholders] |

---

## Hygiene Issues

| Issue | Count | Deals | Action |
|-------|-------|-------|--------|
| Missing close date | [X] | [List] | Add realistic close dates |
| Missing amount | [X] | [List] | Estimate or qualify |
| Missing next step | [X] | [List] | Define next action |
| No primary contact | [X] | [List] | Assign contact |

---

## Pipeline Shape

### By Stage
| Stage | # Deals | Value | % of Pipeline |
|-------|---------|-------|---------------|
| [Stage] | [X] | $[X] | [X]% |

### By Close Month
| Month | # Deals | Value |
|-------|---------|-------|
| [Month] | [X] | $[X] |

### By Deal Size
| Size | # Deals | Value |
|------|---------|-------|
| $100K+ | [X] | $[X] |
| $50K-100K | [X] | $[X] |
| $25K-50K | [X] | $[X] |
| <$25K | [X] | $[X] |

---

## Recommendations

### This Week
1. [ ] [Specific action for priority deal 1]
2. [ ] [Action for at-risk deal]
3. [ ] [Hygiene task]

### This Month
1. [ ] [Strategic action]
2. [ ] [Pipeline building if needed]

---

## Deals to Consider Removing

These deals may be dead weight:

| Deal | Amount | Reason | Recommendation |
|------|--------|--------|----------------|
| [Deal] | $[X] | [No activity 60+ days, no response] | Mark closed-lost |
| [Deal] | $[X] | [Pushed 3+ times, no champion] | Qualify out |
```

---

## 优先级排序框架

AI助手将使用以下框架对您的交易进行排序：

| 因素 | 权重 | 评判依据 |
|------|------|---------|
| **关闭日期** | 30% | 最快关闭的交易优先级最高 |
| **交易规模** | 25% | 更大的交易 = 更多关注 |
| **阶段** | 20% | 阶段越后 = 更多关注 |
| **活动** | 15% | 活跃交易优先 |
| **风险** | 10% | 风险越低 = 越安全 |

您可以告知AI助手调整权重："关注大交易而非快速关单" 或 "我需要快赢，优先关注关闭日期。"

---

## 如果已连接 CRM

- AI助手会自动拉取您的管道
- 用新的关闭日期、阶段、下一步更新记录
- 创建跟进任务
- 跨时间跟踪数据卫生改进情况

---

## 使用建议

1. **每周复盘** — 管道健康衰减很快。每周复盘能早发现问题。
2. **清理僵尸交易** — 陈旧商机会虚报管道并扭曲预测。要果断。
3. **所有交易都要多线程** — 如果一个人不回应，您需要备用联系人。
4. **关闭日期要有意义** — 关闭日期应该是您预期签约的日期，而不是您期望签约的日期。
