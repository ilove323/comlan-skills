---
name: sequence-load
description: "查找符合条件的线索并将其批量加入 Apollo 外联序列。在一个流程中处理丰富、联系人创建、去重和入组。触发词：加载序列、导入序列、sequence-load"
user-invocable: true
---

# 加载序列

端到端地查找、丰富联系人并将其加入外联序列。用户通过 "$ARGUMENTS" 提供目标定位条件和序列名称。

## 示例

- `/apollo:sequence-load add 20 VP Sales at SaaS companies to my "Q1 Outbound" sequence`
- `/apollo:sequence-load SDR managers at fintech startups → Cold Outreach v2`
- `/apollo:sequence-load list sequences`（显示所有可用序列）
- `/apollo:sequence-load directors of engineering, 500+ employees, US → Demo Follow-up`
- `/apollo:sequence-load reload 15 more leads into "Enterprise Pipeline"`

## 第一步 — 解析输入

从 "$ARGUMENTS" 中提取：

**目标定位条件：**
- 职位 → `person_titles`
- 级别 → `person_seniorities`
- 行业关键词 → `q_organization_keyword_tags`
- 公司规模 → `organization_num_employees_ranges`
- 地点 → `person_locations` 或 `organization_locations`

**序列信息：**
- 序列名称（"to"、"into" 或 "→" 之后的文本）
- 数量 — 需要添加多少联系人（如未指定，默认为 10）

如果用户仅说 "list sequences"，跳至第二步并显示所有可用序列。

## 第二步 — 查找序列

使用 `mcp__claude_ai_Apollo_MCP__apollo_emailer_campaigns_search` 查找目标序列：
- 将 `q_name` 设为输入中的序列名称

如果无匹配或有多个匹配：
- 以表格形式展示所有可用序列：| 名称 | ID | 状态 |
- 请用户选择一个

## 第三步 — 获取邮件账户

使用 `mcp__claude_ai_Apollo_MCP__apollo_email_accounts_index` 列出已绑定的邮件账户。

- 如果只有一个账户 → 自动使用
- 如果有多个 → 展示并询问使用哪个发送

## 第四步 — 查找匹配人员

使用 `mcp__claude_ai_Apollo_MCP__apollo_mixed_people_api_search` 配合目标定位条件。
- 将 `per_page` 设为所需数量（或默认 10）

以预览表格形式展示候选人：

| # | 姓名 | 职位 | 公司 | 所在地 |
|---|---|---|---|---|

询问：**"将这 [N] 位联系人加入 [序列名称]？此操作将消耗 [N] 个 Apollo 积分用于丰富。"**

等待用户确认后再继续。

## 第五步 — 丰富并创建联系人

对每位已确认的线索：

1. **丰富** — 使用 `mcp__claude_ai_Apollo_MCP__apollo_people_bulk_match`（每次调用最多 10 人），传入：
   - 每位联系人的 `first_name`、`last_name`、`domain`
   - 将 `reveal_personal_emails` 设为 `true`

2. **创建联系人** — 对每位已丰富的联系人，使用 `mcp__claude_ai_Apollo_MCP__apollo_contacts_create`，传入：
   - `first_name`、`last_name`、`email`、`title`、`organization_name`
   - 如有，传入 `direct_phone` 或 `mobile_phone`
   - 将 `run_dedupe` 设为 `true`

收集所有已创建的联系人 ID。

## 第六步 — 加入序列

使用 `mcp__claude_ai_Apollo_MCP__apollo_emailer_campaigns_add_contact_ids`，传入：
- `id`：序列 ID
- `emailer_campaign_id`：相同的序列 ID
- `contact_ids`：已创建联系人 ID 的数组
- `send_email_from_email_account_id`：所选邮件账户 ID
- `sequence_active_in_other_campaigns`：`false`（安全默认值）

## 第七步 — 确认入组

展示摘要：

---

**序列加载成功**

| 字段 | 值 |
|---|---|
| 序列 | [名称] |
| 已添加联系人 | [数量] |
| 发送账户 | [邮箱地址] |
| 消耗积分 | [数量] |

**已入组联系人：**

| 姓名 | 职位 | 公司 | 邮箱 |
|---|---|---|---|

---

## 第八步 — 提供后续行动建议

询问用户：

1. **继续加载** — 查找并添加下一批线索
2. **查看序列** — 展示序列详情和所有已入组联系人
3. **移除联系人** — 使用 `mcp__claude_ai_Apollo_MCP__apollo_emailer_campaigns_remove_or_stop_contact_ids` 移除特定联系人
4. **暂停联系人** — 使用 `status: "paused"` 和 `auto_unpause_at` 日期重新添加
