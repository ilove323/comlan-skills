---
name: memory-management
description: 让 AI助手成为真正工作场所协作者的两层记忆系统。解码缩写、首字母缩写、昵称和内部语言，使 AI助手像同事一样理解请求。触发词：记忆管理、上下文管理、memory-management
user-invocable: false
---

# 记忆管理

记忆让 AI助手成为您的工作场所协作者——一个懂得您内部语言的人。

## 目标

将缩写转化为理解：

```
用户："让 todd 为 oracle 做 PSR"
              ↓ AI助手解码
"请 Todd Martinez（财务负责人）为 Oracle Systems 项目
 （230 万美元，Q2 结束）准备流水线状态报告"
```

没有记忆，这个请求毫无意义。有了记忆，AI助手知道：
- **todd** → Todd Martinez，财务负责人，偏好 Slack
- **PSR** → 流水线状态报告（每周销售文档）
- **oracle** → Oracle Systems 项目，不是那家公司

## 架构

```
CLAUDE.md          ← 热缓存（约 30 人，常用术语）
memory/
  glossary.md      ← 完整解码表（所有内容）
  people/          ← 完整档案
  projects/        ← 项目详情
  context/         ← 公司、团队、工具
```

**CLAUDE.md（热缓存）：**
- 您最常互动的约 30 人
- 约 30 个最常用的缩写/术语
- 活跃项目（5-15 个）
- 您的偏好
- **目标：覆盖 90% 的日常解码需求**

**memory/glossary.md（完整词汇表）：**
- 完整解码表——所有人，所有术语
- 当 CLAUDE.md 中找不到时搜索
- 可无限增长

**memory/people/、projects/、context/：**
- 执行时需要丰富细节
- 完整档案、历史记录、背景

## 查找流程

```
用户："询问 todd 关于 phoenix 的 PSR"

1. 检查 CLAUDE.md（热缓存）
   → Todd？✓ Todd Martinez，财务
   → PSR？✓ 流水线状态报告
   → Phoenix？✓ 数据库迁移项目

2. 如未找到 → 搜索 memory/glossary.md
   → 完整词汇表包含所有人/所有内容

3. 如仍未找到 → 询问用户
   → "X 是什么意思？我会记住它。"
```

这种分层方法保持 CLAUDE.md 精简（约 100 行），同时在 memory/ 中支持无限扩展。

## 文件位置

- **工作记忆：** 当前工作目录中的 `CLAUDE.md`
- **深度记忆：** `memory/` 子目录

## 工作记忆格式（CLAUDE.md）

使用表格以节省空间。目标总计约 50-80 行。

```markdown
# 记忆

## 我
[姓名]，[团队] 的 [角色]。[一句话描述我的工作。]

## 人员
| 谁 | 角色 |
|-----|------|
| **Todd** | Todd Martinez，财务负责人 |
| **Sarah** | Sarah Chen，工程（平台） |
| **Greg** | Greg Wilson，销售 |
→ 完整列表：memory/glossary.md，档案：memory/people/

## 术语
| 术语 | 含义 |
|------|---------|
| PSR | 流水线状态报告 |
| P0 | 立即处理优先级 |
| standup | 每日 9 点同步 |
→ 完整词汇表：memory/glossary.md

## 项目
| 名称 | 内容 |
|------|------|
| **Phoenix** | 数据库迁移，Q2 上线 |
| **Horizon** | 移动应用重设计 |
→ 详情：memory/projects/

## 偏好
- 25 分钟会议，留缓冲时间
- 异步优先，Slack 优于邮件
- 周五下午不开会
```

## 深度记忆格式（memory/）

**memory/glossary.md** - 解码表：
```markdown
# 词汇表

工作场所缩写、首字母缩写和内部语言。

## 缩写
| 术语 | 含义 | 背景 |
|------|---------|---------|
| PSR | 流水线状态报告 | 每周销售文档 |
| OKR | 目标与关键结果 | 季度规划 |
| P0/P1/P2 | 优先级 | P0 = 立即处理 |

## 内部术语
| 术语 | 含义 |
|------|---------|
| standup | #engineering 的每日 9 点同步 |
| the migration | Project Phoenix 数据库工作 |
| ship it | 部署到生产环境 |
| escalate | 上报给领导层 |

## 昵称 → 全名
| 昵称 | 人员 |
|----------|--------|
| Todd | Todd Martinez（财务） |
| T | 也是 Todd Martinez |

## 项目代号
| 代号 | 项目 |
|----------|---------|
| Phoenix | 数据库迁移 |
| Horizon | 新移动应用 |
```

**memory/people/{name}.md：**
```markdown
# Todd Martinez

**别名：** Todd，T
**角色：** 财务负责人
**团队：** 财务
**汇报给：** CFO（Michael Chen）

## 沟通方式
- 偏好 Slack 私信
- 回复快速，非常直接
- 最佳联系时间：上午

## 背景
- 负责所有 PSR 和财务报告
- 超过 50 万美元的交易审批关键联系人
- 与销售团队密切合作进行预测

## 备注
- 小熊队球迷，喜欢聊棒球
```

**memory/projects/{name}.md：**
```markdown
# Project Phoenix

**代号：** Phoenix
**别称：** "the migration"
**状态：** 活跃，Q2 上线

## 内容
数据库从旧版 Oracle 迁移到 PostgreSQL。

## 关键人员
- Sarah - 技术负责人
- Todd - 预算负责人
- Greg - 利益相关方（销售影响）

## 背景
预算 120 万美元，6 个月时间表。Horizon 项目的关键路径。
```

**memory/context/company.md：**
```markdown
# 公司背景

## 工具与系统
| 工具 | 用途 | 内部名称 |
|------|----------|---------------|
| Slack | 沟通 | - |
| Asana | 工程任务 | - |
| Salesforce | CRM | "SF" 或 "the CRM" |
| Notion | 文档/Wiki | - |

## 团队
| 团队 | 职能 | 关键人员 |
|------|--------------|------------|
| Platform | 基础设施 | Sarah（负责人） |
| Finance | 财务 | Todd（负责人） |
| Sales | 营收 | Greg |

## 流程
| 流程 | 含义 |
|---------|---------------|
| Weekly sync | 周一 10 点全员会议 |
| Ship review | 周四部署审批 |
```

## 如何交互

### 解码用户输入（分层查找）

在处理请求之前**始终**解码缩写：

```
1. CLAUDE.md（热缓存）     → 先检查，覆盖 90% 的情况
2. memory/glossary.md        → 如热缓存中没有，查完整词汇表
3. memory/people/、projects/ → 需要时获取丰富细节
4. 询问用户                  → 未知术语？学习它。
```

示例：
```
用户："让 todd 为 oracle 做 PSR"

CLAUDE.md 查找：
  "todd" → Todd Martinez，财务 ✓
  "PSR" → 流水线状态报告 ✓
  "oracle" → （热缓存中没有）

memory/glossary.md 查找：
  "oracle" → Oracle Systems 项目（230 万美元）✓

现在 AI助手可以在完整背景下行动了。
```

### 添加记忆

当用户说"记住这个"或"X 的意思是 Y"时：

1. **词汇表条目**（缩写、术语、缩写词）：
   - 添加到 memory/glossary.md
   - 如果常用，添加到 CLAUDE.md 快速词汇表

2. **人员：**
   - 创建/更新 memory/people/{name}.md
   - 如果重要，添加到 CLAUDE.md 关键人员
   - **记录昵称** - 对解码至关重要

3. **项目：**
   - 创建/更新 memory/projects/{name}.md
   - 如果当前活跃，添加到 CLAUDE.md 活跃项目
   - **记录代号** - "Phoenix"、"the migration" 等

4. **偏好：** 添加到 CLAUDE.md 偏好部分

### 回忆记忆

当用户问"X 是谁"或"X 是什么意思"时：

1. 先检查 CLAUDE.md
2. 检查 memory/ 获取完整详情
3. 如未找到："我还不知道 X 是什么意思。您能告诉我吗？"

### 渐进式披露

1. 加载 CLAUDE.md 以快速解析任何请求
2. 需要执行时深入 memory/ 获取完整背景
3. 示例：为 todd 起草关于 PSR 的邮件
   - CLAUDE.md 告诉您 Todd = Todd Martinez，PSR = 流水线状态报告
   - memory/people/todd-martinez.md 告诉您他偏好 Slack，说话直接

## 初始化

使用 `/productivity:start` 通过扫描您的聊天、日历、邮件和文档来初始化。提取人员、项目并开始构建词汇表。

## 约定

- 在 CLAUDE.md 中用**粗体**标注术语以便扫描
- 保持 CLAUDE.md 在约 100 行以内（"热 30"规则）
- 文件名：小写，用连字符（`todd-martinez.md`、`project-phoenix.md`）
- 始终记录昵称和别名
- 使用词汇表表格便于查找
- 当某内容频繁使用时，将其提升到 CLAUDE.md
- 当某内容过时时，将其降级到仅存于 memory/

## 存储位置

| 类型 | CLAUDE.md（热缓存） | memory/（完整存储） |
|------|----------------------|------------------------|
| 人员 | 约 30 个最常联系的人 | glossary.md + people/{name}.md |
| 缩写/术语 | 约 30 个最常用的 | glossary.md（完整列表） |
| 项目 | 仅活跃项目 | glossary.md + projects/{name}.md |
| 昵称 | 如在前 30 名则在关键人员中 | glossary.md（所有昵称） |
| 公司背景 | 仅快速参考 | context/company.md |
| 偏好 | 所有偏好 | - |
| 历史/过时 | ✗ 删除 | ✓ 保留在 memory/ |

## 提升 / 降级

**提升到 CLAUDE.md 的条件：**
- 您频繁使用某术语/人员
- 它是当前工作的一部分

**降级到仅 memory/ 的条件：**
- 项目已完成
- 人员不再是常联系人
- 术语很少使用

这让 CLAUDE.md 保持新鲜和相关。
