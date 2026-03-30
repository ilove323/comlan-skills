---
name: account-research
description: 调研公司或人员，获取可落地的销售情报。默认使用网络搜索，接入数据富化工具或 CRM 后效果更强。触发词：客户调研、账户研究、account research
---

# 客户调研

在外联之前全面了解任何公司或人员。本技能始终通过网络搜索提供基础信息，接入富化和 CRM 数据后效果显著提升。

## 工作原理

```
┌─────────────────────────────────────────────────────────────────┐
│                     ACCOUNT RESEARCH                             │
├─────────────────────────────────────────────────────────────────┤
│  始终可用（通过网络搜索独立运行）                                  │
│  ✓ 公司概览：业务、规模、行业                                     │
│  ✓ 近期新闻：融资、管理层变动、重要公告                           │
│  ✓ 招聘信号：在招职位、增长指标                                   │
│  ✓ 关键人员：LinkedIn 上的领导团队                                │
│  ✓ 产品/服务：所售内容及服务对象                                  │
├─────────────────────────────────────────────────────────────────┤
│  增强效果（接入工具后）                                           │
│  + 数据富化：已验证邮件、电话、技术栈、组织架构                   │
│  + CRM：既往关系、过往商机、联系人记录                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## 快速开始

只需告诉AI助手要调研谁：

- "Research Stripe"
- "Look up the CTO at Notion"
- "Intel on acme.com"
- "Who is Sarah Chen at TechCorp?"
- "Tell me about [company] before my call"

AI助手会立即进行网络搜索。如果已连接富化工具或 CRM，也会同步提取那里的数据。

---

## 连接器（可选）

连接您的工具来增强本技能：

| 连接器 | 新增功能 |
|--------|---------|
| **数据富化** | 已验证邮件、电话号码、技术栈、组织架构、融资详情 |
| **CRM** | 既往关系历史、过往商机、现有联系人、备注 |

> **没有连接器？** 没关系。网络搜索已能为任何公司或人员提供扎实的调研结果。

---

## 输出格式

```markdown
# Research: [Company or Person Name]

**Generated:** [Date]
**Sources:** Web Search [+ Enrichment] [+ CRM]

---

## Quick Take

[2-3 sentences: Who they are, why they might need you, best angle for outreach]

---

## Company Profile

| Field | Value |
|-------|-------|
| **Company** | [Name] |
| **Website** | [URL] |
| **Industry** | [Industry] |
| **Size** | [Employee count] |
| **Headquarters** | [Location] |
| **Founded** | [Year] |
| **Funding** | [Stage + amount if known] |
| **Revenue** | [Estimate if available] |

### What They Do
[1-2 sentence description of their business, product, and customers]

### Recent News
- **[Headline]** — [Date] — [Why it matters for your outreach]
- **[Headline]** — [Date] — [Why it matters]

### Hiring Signals
- [X] open roles in [Department]
- Notable: [Relevant roles like Engineering, Sales, AI/ML]
- Growth indicator: [Hiring velocity interpretation]

---

## Key People

### [Name] — [Title]
| Field | Detail |
|-------|--------|
| **LinkedIn** | [URL] |
| **Background** | [Prior companies, education] |
| **Tenure** | [Time at company] |
| **Email** | [If enrichment connected] |

**Talking Points:**
- [Personal hook based on background]
- [Professional hook based on role]

[Repeat for relevant contacts]

---

## Tech Stack [If Enrichment Connected]

| Category | Tools |
|----------|-------|
| **Cloud** | [AWS, GCP, Azure, etc.] |
| **Data** | [Snowflake, Databricks, etc.] |
| **CRM** | [e.g. Salesforce, HubSpot] |
| **Other** | [Relevant tools] |

**Integration Opportunity:** [How your product fits with their stack]

---

## Prior Relationship [If CRM Connected]

| Field | Detail |
|-------|--------|
| **Status** | [New / Prior prospect / Customer / Churned] |
| **Last Contact** | [Date and type] |
| **Previous Opps** | [Won/Lost and why] |
| **Known Contacts** | [Names already in CRM] |

**History:** [Summary of past relationship]

---

## Qualification Signals

### Positive Signals
- ✅ [Signal and evidence]
- ✅ [Signal and evidence]

### Potential Concerns
- ⚠️ [Concern and what to watch for]

### Unknown (Ask in Discovery)
- ❓ [Gap in understanding]

---

## Recommended Approach

**Best Entry Point:** [Person and why]

**Opening Hook:** [What to lead with based on research]

**Discovery Questions:**
1. [Question about their situation]
2. [Question about pain points]
3. [Question about decision process]

---

## Sources
- [Source 1](URL)
- [Source 2](URL)
```

---

## 执行流程

### 第一步：解析请求

```
识别调研对象：
- "Research Stripe" → 公司调研
- "Look up John Smith at Acme" → 人员 + 公司
- "Who is the CTO at Notion" → 基于职位的搜索
- "Intel on acme.com" → 基于域名的查找
```

### 第二步：网络搜索（始终执行）

```
执行以下搜索：
1. "[Company name]" → 首页、关于页面
2. "[Company name] news" → 近期公告
3. "[Company name] funding" → 融资历史
4. "[Company name] careers" → 招聘信号
5. "[Person name] [Company] LinkedIn" → 个人资料
6. "[Company name] product" → 所售产品
7. "[Company name] customers" → 服务对象
```

**提取内容：**
- 公司描述与定位
- 近期新闻（90 天内）
- 领导团队
- 在招职位
- 技术相关提及
- 客户群体

### 第三步：数据富化（如已连接）

```
如果富化工具可用：
1. 富化公司 → 公司基本信息、融资情况、技术栈
2. 搜索人员 → 组织架构、联系人列表
3. 富化人员 → 邮件、电话、背景
4. 获取信号 → 意向数据、招聘速度
```

**富化数据新增内容：**
- 已验证联系方式
- 完整组织架构
- 精确员工人数
- 详细技术栈
- 含投资方的融资历史

### 第四步：CRM 查询（如已连接）

```
如果 CRM 可用：
1. 按域名搜索账户
2. 获取相关联系人
3. 获取商机历史
4. 获取活动时间线
```

**CRM 数据新增内容：**
- 既往关系背景
- 此前发生的情况（赢/输的交易）
- 已沟通过的联系人
- 备注与历史记录

### 第五步：综合分析

```
1. 合并所有来源信息
2. 优先采用富化数据（更准确）
3. 如有 CRM 背景则添加
4. 识别资质信号
5. 生成谈话要点
6. 推荐接触策略
```

---

## 调研变体

### 公司调研
重点关注：业务概览、新闻、招聘、领导层

### 人员调研
重点关注：背景、职位、LinkedIn 动态、谈话要点

### 竞品调研
重点关注：产品对比、定位、赢/输规律

### 会前调研
重点关注：与会者背景、近期新闻、关系历史

---

## 获得更好调研结果的建议

1. **提供域名** — "research acme.com" 更精确
2. **指定人员** — "look up Jane Smith, VP Sales at Acme"
3. **说明目标** — "research Stripe before my demo call"
4. **追问细节** — 初步调研后可问 "what's their tech stack?"

---

## 相关技能

- **call-prep** — 结合本调研进行完整的会议准备
- **draft-outreach** — 基于调研撰写个性化消息
- **prospecting** — 资质审核并排序调研目标
