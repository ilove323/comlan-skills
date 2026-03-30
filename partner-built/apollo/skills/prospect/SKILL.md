---
name: prospect
description: "完整的 ICP 到线索转化流程。用自然语言描述您的理想客户，获取一份包含邮件和电话号码的排名丰富决策者线索列表。触发词：挖掘潜客、潜在客户挖掘、prospect"
user-invocable: true
---

# 挖掘潜客

从 ICP 描述一步到位地生成经过排名和丰富的线索列表。用户通过 "$ARGUMENTS" 描述其理想客户。

## 示例

- `/apollo:prospect VP of Engineering at Series B+ SaaS companies in the US, 200-1000 employees`
- `/apollo:prospect heads of marketing at e-commerce companies in Europe`
- `/apollo:prospect CTOs at fintech startups, 50-500 employees, New York`
- `/apollo:prospect procurement managers at manufacturing companies with 1000+ employees`
- `/apollo:prospect SDR leaders at companies using Salesforce and Outreach`

## 第一步 — 解析 ICP

从 "$ARGUMENTS" 中的自然语言描述提取结构化筛选条件：

**公司筛选条件：**
- 行业/垂直领域关键词 → `q_organization_keyword_tags`
- 员工人数范围 → `organization_num_employees_ranges`
- 公司所在地 → `organization_locations`
- 特定域名 → `q_organization_domains_list`

**人员筛选条件：**
- 职位 → `person_titles`
- 级别 → `person_seniorities`
- 人员所在地 → `person_locations`

如果 ICP 描述模糊，在继续之前提出 1-2 个澄清问题。至少需要职位/角色和行业或公司规模信息。

## 第二步 — 搜索公司

使用 `mcp__claude_ai_Apollo_MCP__apollo_mixed_companies_search` 配合公司筛选条件：
- `q_organization_keyword_tags` 筛选行业/垂直领域
- `organization_num_employees_ranges` 筛选规模
- `organization_locations` 筛选地域
- 将 `per_page` 设为 25

## 第三步 — 丰富头部公司信息

使用 `mcp__claude_ai_Apollo_MCP__apollo_organizations_bulk_enrich`，传入前 10 条结果的域名。此操作将获取营收、融资、人数及企业画像数据，用于公司排名。

## 第四步 — 查找决策者

使用 `mcp__claude_ai_Apollo_MCP__apollo_mixed_people_api_search`，配合：
- 来自 ICP 的 `person_titles` 和 `person_seniorities`
- 限定于已丰富公司域名的 `q_organization_domains_list`
- 将 `per_page` 设为 25

## 第五步 — 丰富头部线索

> **积分提醒**：在继续之前，告知用户将消耗的确切积分数量。

使用 `mcp__claude_ai_Apollo_MCP__apollo_people_bulk_match`，每次调用最多丰富 10 条线索，传入：
- 每位联系人的 `first_name`、`last_name`、`domain`
- 将 `reveal_personal_emails` 设为 `true`

如果线索超过 10 条，分批次调用。

## 第六步 — 展示线索列表

以排名表格形式展示结果：

### 符合以下 ICP 的线索：[ICP 摘要]

| # | 姓名 | 职位 | 公司 | 员工数 | 营收 | 邮箱 | 电话 | ICP 匹配度 |
|---|---|---|---|---|---|---|---|---|

**ICP 匹配度**评分标准：
- **强** — 职位、级别、公司规模和行业全部匹配
- **良** — 4 项中符合 3 项
- **部分** — 4 项中符合 2 项

**摘要**：找到 X 条线索，分布于 Y 家公司。消耗 Z 个积分。

## 第七步 — 提供后续行动建议

询问用户：

1. **全部保存到 Apollo** — 对每条线索使用 `mcp__claude_ai_Apollo_MCP__apollo_contacts_create`（设置 `run_dedupe: true`）批量创建联系人
2. **加入序列** — 询问使用哪个序列，然后对这些联系人执行 sequence-load 流程
3. **深入了解某公司** — 对列表中任意公司运行 `/apollo:company-intel`
4. **优化搜索** — 调整过滤条件并重新搜索
5. **导出** — 将线索格式化为 CSV 样式表格以便复制粘贴
