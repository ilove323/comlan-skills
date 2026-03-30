---
name: enrich-lead
description: "即时线索丰富。输入姓名、公司、LinkedIn 链接或邮箱，获取包含邮件、电话、职位、公司信息及后续行动建议的完整联系人档案。触发词：丰富线索、线索enrichment、enrich-lead"
user-invocable: true
---

# 丰富线索

将任意标识符转化为完整的联系人档案。用户通过 "$ARGUMENTS" 提供识别信息。

## 示例

- `/apollo:enrich-lead Tim Zheng at Apollo`
- `/apollo:enrich-lead https://www.linkedin.com/in/timzheng`
- `/apollo:enrich-lead sarah@stripe.com`
- `/apollo:enrich-lead Jane Smith, VP Engineering, Notion`
- `/apollo:enrich-lead CEO of Figma`

## 第一步 — 解析输入

从 "$ARGUMENTS" 中提取所有可用的标识符：
- 名字、姓氏
- 公司名称或域名
- LinkedIn 链接
- 邮箱地址
- 职位（用作匹配提示）

如果输入模糊（例如仅 "CEO of Figma"），先使用 `mcp__claude_ai_Apollo_MCP__apollo_mixed_people_api_search` 配合相关职位和域名过滤条件查找人员，然后再进行丰富操作。

## 第二步 — 丰富该联系人

> **积分提醒**：在调用接口前，告知用户丰富操作将消耗 1 个 Apollo 积分。

使用 `mcp__claude_ai_Apollo_MCP__apollo_people_match` 并提供所有可用标识符：
- `first_name`、`last_name`（如知道姓名）
- `domain` 或 `organization_name`（如知道公司）
- `linkedin_url`（如提供了 LinkedIn）
- `email`（如提供了邮箱）
- 将 `reveal_personal_emails` 设为 `true`

如果匹配失败，尝试使用 `mcp__claude_ai_Apollo_MCP__apollo_mixed_people_api_search` 配合更宽松的过滤条件，展示前 3 位候选人。请用户选择一位，然后重新丰富。

## 第三步 — 丰富其公司信息

使用 `mcp__claude_ai_Apollo_MCP__apollo_organizations_enrich`，传入该联系人的公司域名，获取企业画像数据。

## 第四步 — 展示联系人档案

按如下格式输出结果：

---

**[全名]** | [职位]
[公司名称] · [行业] · [员工人数] 名员工

| 字段 | 详情 |
|---|---|
| 工作邮箱 | ... |
| 个人邮箱 | ...（如已获取） |
| 直线电话 | ... |
| 手机号码 | ... |
| 公司电话 | ... |
| 所在地 | 城市、省份、国家 |
| LinkedIn | 链接 |
| 公司域名 | ... |
| 公司营收 | 范围 |
| 公司融资 | 累计融资额 |
| 公司总部 | 地点 |

---

## 第五步 — 提供后续行动建议

询问用户希望采取哪项操作：

1. **保存到 Apollo** — 通过 `mcp__claude_ai_Apollo_MCP__apollo_contacts_create`（设置 `run_dedupe: true`）将此联系人创建为客户
2. **加入序列** — 询问使用哪个序列，然后执行 sequence-load 流程
3. **查找同事** — 使用 `mcp__claude_ai_Apollo_MCP__apollo_mixed_people_api_search`（将 `q_organization_domains_list` 设为该公司域名）搜索更多同公司人员
4. **查找相似人员** — 搜索其他公司中具有相同职位和级别的人员
