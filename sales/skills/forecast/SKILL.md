---
name: forecast
description: 生成含最佳/可能/最差情景、承诺与上行空间分解以及差距分析的加权销售预测。适用于准备季度预测会议、从管道 CSV 评估配额差距、决定哪些交易可以承诺或上行，或对比您的数字检查管道覆盖情况。触发词：销售预测、业绩预测、forecast
---

# /forecast

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

生成含风险分析和承诺建议的加权销售预测。

## 用法

```
/forecast [period]
```

生成预测期间：$ARGUMENTS

如果引用了文件：@$1

---

## 工作原理

```
┌─────────────────────────────────────────────────────────────────┐
│                        FORECAST                                  │
├─────────────────────────────────────────────────────────────────┤
│  独立模式（始终可用）                                             │
│  ✓ 上传 CRM 导出的 CSV                                          │
│  ✓ 或粘贴/描述您的管道交易                                       │
│  ✓ 设置配额和时间节点                                            │
│  ✓ 获取按阶段概率的加权预测                                      │
│  ✓ 风险调整后的预测（最佳/可能/最差情景）                         │
│  ✓ 承诺与上行空间分解                                            │
│  ✓ 差距分析和建议                                                │
├─────────────────────────────────────────────────────────────────┤
│  增强效果（接入工具后）                                           │
│  + CRM：自动拉取管道，实时数据                                    │
│  + 按阶段、细分、交易规模划分的历史赢率                           │
│  + 风险评分的活动信号                                             │
│  + 自动刷新和跨时间跟踪                                           │
└─────────────────────────────────────────────────────────────────┘
```

---

## 需要您提供的内容

### 第一步：您的管道数据

**选项 A：上传 CSV**
从 CRM（如 Salesforce、HubSpot）导出您的管道。最低需要：
- 交易/商机名称
- 金额
- 阶段
- 关闭日期

如果有以下信息则更好：
- 负责人（团队预测时）
- 最后活动日期
- 创建日期
- 账户名称

**选项 B：粘贴您的交易**
```
Acme Corp - $50K - Negotiation - closes Jan 31
TechStart - $25K - Demo scheduled - closes Feb 15
BigCo - $100K - Discovery - closes Mar 30
```

**选项 C：描述您的业务范围**
"我有 8 个在谈交易，总计 $400K。其中两个在谈判阶段（$120K），三个在评估阶段（$180K），三个在探索阶段（$100K）。"

### 第二步：您的目标

- **配额**：您的指标是什么？（例如，"本季度 $500K"）
- **时间节点**：期末是什么时候？（例如，"Q1 截至 3 月 31 日"）
- **已关闭**：本期已签了多少？

---

## 输出内容

```markdown
# Sales Forecast: [Period]

**Generated:** [Date]
**Data Source:** [CSV upload / Manual input / CRM]

---

## Summary

| Metric | Value |
|--------|-------|
| **Quota** | $[X] |
| **Closed to Date** | $[X] ([X]% of quota) |
| **Open Pipeline** | $[X] |
| **Weighted Forecast** | $[X] |
| **Gap to Quota** | $[X] |
| **Coverage Ratio** | [X]x |

---

## Forecast Scenarios

| Scenario | Amount | % of Quota | Assumptions |
|----------|--------|------------|-------------|
| **Best Case** | $[X] | [X]% | All deals close as expected |
| **Likely Case** | $[X] | [X]% | Stage-weighted probabilities |
| **Worst Case** | $[X] | [X]% | Only commit deals close |

---

## Pipeline by Stage

| Stage | # Deals | Total Value | Probability | Weighted Value |
|-------|---------|-------------|-------------|----------------|
| Negotiation | [X] | $[X] | 80% | $[X] |
| Proposal | [X] | $[X] | 60% | $[X] |
| Evaluation | [X] | $[X] | 40% | $[X] |
| Discovery | [X] | $[X] | 20% | $[X] |
| **Total** | [X] | $[X] | — | $[X] |

---

## Commit vs. Upside

### Commit (High Confidence)
Deals you'd stake your forecast on:

| Deal | Amount | Stage | Close Date | Why Commit |
|------|--------|-------|------------|------------|
| [Deal] | $[X] | [Stage] | [Date] | [Reason] |

**Total Commit:** $[X]

### Upside (Lower Confidence)
Deals that could close but have risk:

| Deal | Amount | Stage | Close Date | Risk Factor |
|------|--------|-------|------------|-------------|
| [Deal] | $[X] | [Stage] | [Date] | [Risk] |

**Total Upside:** $[X]

---

## Risk Flags

| Deal | Amount | Risk | Recommendation |
|------|--------|------|----------------|
| [Deal] | $[X] | Close date passed | Update close date or move to lost |
| [Deal] | $[X] | No activity in 14+ days | Re-engage or downgrade stage |
| [Deal] | $[X] | Close date this week, still in discovery | Unlikely to close — push out |

---

## Gap Analysis

**To hit quota, you need:** $[X] more

**Options to close the gap:**
1. **Accelerate [Deal]** — Currently [stage], worth $[X]. If you can close by [date], you're at [X]% of quota.
2. **Revive [Stalled Deal]** — Last active [date]. Worth $[X]. Reach out to [contact].
3. **New pipeline needed** — You need $[X] in new opportunities at [X]x coverage to be safe.

---

## Recommendations

1. [ ] [Specific action for highest-impact deal]
2. [ ] [Action for at-risk deal]
3. [ ] [Pipeline generation recommendation if gap exists]
```

---

## 阶段概率（默认值）

如果您未提供自定义概率，AI助手将使用：

| 阶段 | 默认概率 |
|------|---------|
| Closed Won | 100% |
| Negotiation / Contract | 80% |
| Proposal / Quote | 60% |
| Evaluation / Demo | 40% |
| Discovery / Qualification | 20% |
| Prospecting / Lead | 10% |

如果您的阶段或概率不同，请告知AI助手。

---

## 如果已连接 CRM

- AI助手会自动拉取您的管道
- 使用您实际的历史赢率
- 将活动最近性纳入风险评分
- 跨时间跟踪预测变化
- 与之前的预测进行比较

---

## 使用建议

1. **诚实评估承诺** — 只承诺您有把握的交易。上行空间用于其他所有情况。
2. **更新关闭日期** — 陈旧的关闭日期会拖累预测准确性。将无法及时关闭的交易顺延。
3. **管道覆盖率很重要** — 3 倍管道覆盖率是健康的。低于 2 倍有风险。
4. **活动是信号** — 近期无活动的交易风险高于阶段所示。
