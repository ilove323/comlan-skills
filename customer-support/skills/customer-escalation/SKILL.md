---
name: customer-escalation
description: 为工程、产品或管理层打包完整背景的升级材料。适用于 Bug 超出常规支持范围需要工程介入、多个客户报告同一问题、客户威胁流失，或问题已超出 SLA 时限未解决等情况。触发词：客户升级、升级处理、问题升级
---

# /customer-escalation

> 如果遇到不熟悉的占位符或需要查看已连接的工具，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

将支持问题整理为结构化的升级简报，提交给工程、产品或管理层。收集背景信息、整理复现步骤、评估业务影响，并确定合适的升级目标。

## 用法

```
/customer-escalation <issue description> [customer name or account]
```

示例：
- `/customer-escalation API returning 500 errors intermittently for Acme Corp`
- `/customer-escalation Data export is missing rows — 3 customers reported this week`
- `/customer-escalation SSO login loop affecting all Enterprise customers`
- `/customer-escalation Customer threatening to churn over missing audit log feature`

## 工作流程

### 1. 理解问题

解析输入内容并确定：

- **损坏或缺失的内容**：核心技术或产品问题
- **受影响方**：具体客户、客户群或所有用户
- **持续时间**：何时开始？客户等待了多久？
- **已尝试的措施**：任何故障排查或变通方案
- **升级原因**：是什么让这个问题超出了正常支持范围

使用以下"何时升级 vs. 在支持内处理"标准确认是否需要升级。

### 2. 收集背景信息

从可用来源汇总相关信息：

- **~~support platform**：相关工单、沟通时间线、过往排查步骤
- **~~CRM**（如已连接）：账户详情、关键联系人、过往升级记录
- **~~chat**：关于此问题的内部讨论、其他客户的类似反馈
- **~~project tracker**（如已连接）：相关 Bug 报告或功能请求、工程状态
- **~~knowledge base**：已知问题或变通方案、相关文档

### 3. 评估业务影响

根据以下影响维度量化：

- **广度**：受影响的客户/用户有多少？还在增长吗？
- **深度**：完全阻塞 vs. 带来不便？
- **持续时间**：此问题已持续多久？
- **收入影响**：有 ARR 风险吗？影响到待签交易吗？
- **时间压力**：有硬性截止日期吗？

### 4. 确定升级目标

根据以下升级层级，确定合适的目标：二线支持、工程、产品、安全或管理层。

### 5. 整理复现步骤（针对 Bug）

如果问题是 Bug，请按照以下复现步骤最佳实践，记录清晰的复现步骤，包含环境详情和证据。

### 6. 生成升级简报

```
## ESCALATION: [One-line summary]

**Severity:** [Critical / High / Medium]
**Target team:** [Engineering / Product / Security / Leadership]
**Reported by:** [Your name/team]
**Date:** [Today's date]

### Impact
- **Customers affected:** [Who and how many]
- **Workflow impact:** [What they can't do]
- **Revenue at risk:** [If applicable]
- **Time in queue:** [How long this has been an issue]

### Issue Description
[Clear, concise description of the problem — 3-5 sentences]

### What's Been Tried
1. [Troubleshooting step and result]
2. [Troubleshooting step and result]
3. [Troubleshooting step and result]

### Reproduction Steps
[If applicable — follow the format below]
1. [Step]
2. [Step]
3. [Step]
Expected: [X]
Actual: [Y]
Environment: [Details]

### Customer Communication
- **Last update to customer:** [Date and what was communicated]
- **Customer expectation:** [What they're expecting and by when]
- **Escalation risk:** [Will they escalate further if not resolved by X?]

### What's Needed
- [Specific ask — "investigate root cause", "prioritize fix",
  "make product decision on X", "approve exception for Y"]
- **Deadline:** [When this needs resolution or an update]

### Supporting Context
- [Related tickets or links]
- [Internal discussion threads]
- [Documentation or logs]
```

### 7. 提供后续步骤

生成升级材料后：
- "需要我将此内容发布到目标团队的 ~~chat 频道吗？"
- "需要我向客户发送一封过渡性回复吗？"
- "需要我设置跟进提醒来检查进展吗？"
- "需要我起草一封包含当前状态的面向客户的更新邮件吗？"

---

## 何时升级 vs. 在支持内处理

### 在支持内处理的情况：
- 问题有已记录的解决方案或已知变通方案
- 这是一个您可以解决的配置或设置问题
- 客户需要指导或培训，而非修复
- 问题是已知限制，有已记录的替代方案
- 过往类似工单在支持层面得到了解决

### 升级的情况：
- **技术层面**：Bug 已确认需要代码修复、需要基础设施调查、存在数据损坏或丢失
- **复杂度**：问题超出支持的诊断能力、需要支持没有的访问权限、涉及自定义实现
- **影响层面**：多个客户受影响、生产系统宕机、数据完整性风险、安全顾虑
- **业务层面**：高价值客户面临风险、SLA 即将/已经违约、客户要求高管介入
- **时间层面**：问题已超出 SLA 时限、客户等待时间过长、常规支持渠道没有进展
- **规律层面**：3 个以上客户报告同一问题、据称已修复的问题再次出现、严重程度随时间加剧

## 升级层级

### 一线 → 二线（支持升级）
**来自：** 一线支持
**至：** 高级支持/技术支持专家
**时机：** 问题需要深入调查、专业产品知识或高级故障排查
**包含内容：** 工单摘要、已尝试的步骤、客户背景

### 二线 → 工程
**来自：** 高级支持
**至：** 工程团队（相关产品领域）
**时机：** 已确认的 Bug、基础设施问题、需要代码变更、需要系统级调查
**包含内容：** 完整复现步骤、环境详情、日志或错误信息、业务影响、客户时间线

### 二线 → 产品
**来自：** 高级支持
**至：** 产品管理
**时机：** 功能缺口导致客户痛点、需要设计决策、工作流与客户预期不符、竞争性客户需求需要排优先级
**包含内容：** 客户使用场景、业务影响、需求频率、竞争压力（如已知）

### 任意 → 安全
**来自：** 任意支持层级
**至：** 安全团队
**时机：** 潜在数据泄露、未经授权访问、漏洞报告、合规顾虑
**包含内容：** 观察到的情况、可能受影响的对象、已采取的即时遏制措施、紧急程度评估
**注意：** 安全升级绕过正常层级——无论您处于哪个级别，都应立即升级

### 任意 → 管理层
**来自：** 任意层级（通常是二线或经理）
**至：** 支持管理层、高管团队
**时机：** 高价值客户威胁流失、重要账户 SLA 违约、需要跨职能决策、需要政策例外、有 PR 或法律风险
**包含内容：** 完整业务背景、风险收入、已尝试的措施、需要的具体决策或行动、截止日期

## 业务影响评估

升级时，尽量量化影响：

### 影响维度

| 维度 | 需要回答的问题 |
|------|--------------|
| **广度** | 有多少客户/用户受影响？还在增长吗？ |
| **深度** | 影响有多严重？完全阻塞 vs. 带来不便？ |
| **持续时间** | 已持续多久？什么时候会变得紧急？ |
| **收入** | 面临风险的 ARR 是多少？有待签交易受影响吗？ |
| **声誉** | 这可能会公开吗？是参考客户吗？ |
| **合同** | 是否违反了 SLA？是否有合同义务？ |

### 严重程度速查

- **紧急**：生产宕机、数据面临风险、安全漏洞，或多个高价值客户受影响。需要立即处理。
- **高**：主要功能中断、关键客户被阻塞、SLA 面临风险。需要当天处理。
- **中**：有变通方案的重大问题，重要但不紧急的业务影响。需要本周处理。

## 编写复现步骤

好的复现步骤是 Bug 升级中最有价值的内容。遵循以下实践：

1. **从干净状态开始**：描述起始点（账户类型、配置、权限）
2. **具体明确**："点击 Dashboard 页面右上角的 Export 按钮"而非"尝试导出"
3. **包含精确值**：使用具体输入、日期、ID——而非"输入一些数据"
4. **记录环境**：浏览器、操作系统、账户类型、功能开关、套餐级别
5. **说明频率**：总是可复现？偶发？仅在特定条件下？
6. **附上证据**：截图、错误信息（精确文本）、网络日志、控制台输出
7. **注明已排除项**："在 Chrome 和 Firefox 中测试过——行为相同" "不是账户特定的——在测试账户上也复现了"

## 升级后的跟进节奏

不要升级了就不管了。保持对客户关系的主导权。

| 严重程度 | 内部跟进 | 客户更新 |
|---------|---------|---------|
| **紧急** | 每 2 小时 | 每 2-4 小时（或按 SLA） |
| **高** | 每 4 小时 | 每 4-8 小时 |
| **中** | 每天 | 每 1-2 个工作日 |

### 跟进操作
- 向接收团队确认进展
- 即使没有新信息也要更新客户（"我们仍在调查中——以下是目前了解到的情况"）
- 如果情况有变（好转或恶化），调整严重程度
- 在工单中记录所有更新，形成审计追踪
- 解决后完成闭环：与客户确认、更新内部跟踪、总结经验教训

## 降级处理

不是所有升级都会一直保持升级状态。以下情况可以降级：
- 找到根本原因，且该问题在支持层面可解决
- 找到能解除客户阻塞的变通方案
- 问题自行解决（但仍需记录根本原因）
- 新信息改变了严重程度评估

降级时：
- 通知您已升级的团队
- 在工单中更新解决情况
- 告知客户解决结果
- 记录所学内容供将来参考

## 升级最佳实践

1. 始终量化影响——模糊的升级会被降低优先级
2. 对 Bug 附上复现步骤——这是工程最需要的内容
3. 明确说明您需要什么——"调查"、"修复"、"决策"是不同的诉求
4. 设定并传达截止日期——没有截止日期的紧急性是模糊的
5. 即使升级了技术问题，也要保持对客户关系的主导权
6. 主动跟进——不要等接收团队来找您
7. 记录一切——升级追踪对模式识别和流程改进很有价值
