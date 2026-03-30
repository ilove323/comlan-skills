---
name: competitive-intelligence
description: 调研竞争对手并构建交互式竞品手册。输出带可点击竞品卡片和对比矩阵的 HTML 工件。触发词：竞情分析、竞争情报、competitive intelligence
---

# 竞争情报

全面调研您的竞争对手，生成可在交易中使用的**交互式 HTML 竞品手册**。输出结果是一个带可点击竞品标签页和整体对比矩阵的独立工件。

## 工作原理

```
┌─────────────────────────────────────────────────────────────────┐
│                  COMPETITIVE INTELLIGENCE                        │
├─────────────────────────────────────────────────────────────────┤
│  始终可用（通过网络搜索独立运行）                                  │
│  ✓ 竞品深度分析：功能、定价、市场定位                             │
│  ✓ 近期发布：过去 90 天内发布的内容                              │
│  ✓ 我方近期发布：您发布了哪些可以对抗的内容                       │
│  ✓ 差异化矩阵：您赢在哪里 vs. 对手赢在哪里                       │
│  ✓ 销售话术：如何针对每个竞争对手进行定位                         │
│  ✓ 埋雷问题：自然地暴露对手弱点的问题                            │
├─────────────────────────────────────────────────────────────────┤
│  输出：交互式 HTML 竞品手册                                      │
│  ✓ 对比矩阵总览                                                  │
│  ✓ 每个竞品可点击展开                                            │
│  ✓ 深色主题，专业排版                                            │
│  ✓ 独立 HTML 文件——可分享或托管于任意位置                        │
├─────────────────────────────────────────────────────────────────┤
│  增强效果（接入工具后）                                           │
│  + CRM：赢/输数据、已结束交易中的竞品提及                        │
│  + 文档：现有竞品手册、竞争策略文档                               │
│  + 即时通讯：内部情报、同事的一线反馈                             │
│  + 转录：客户通话中的竞品提及                                     │
└─────────────────────────────────────────────────────────────────┘
```

---

## 快速开始

运行本技能时，AI助手会询问背景信息：

**必填项：**
- 您在哪家公司工作？（或AI助手通过您的邮件识别）
- 主要竞争对手有哪些？（1-5 个名称）

**可选项：**
- 您想先重点分析哪个竞争对手？
- 是否有正在与他们竞争的具体交易？
- 您从客户处听到过关于竞争对手的哪些痛点？

如果AI助手在之前的会话中已有您的销售方背景，会予以确认并跳过提问。

---

## 连接器（可选）

| 连接器 | 新增功能 |
|--------|---------|
| **CRM** | 针对每个竞争对手的赢/输历史、交易级别的竞品跟踪 |
| **文档** | 现有竞品手册、产品对比文档、竞争策略文档 |
| **即时通讯** | 内部聊天情报（如 Slack）——团队从市场一线听到的信息 |
| **转录** | 客户通话中的竞品提及、已提出的异议 |

> **没有连接器？** 网络调研效果很好。AI助手会从公开来源提取一切——产品页面、定价、博客、发布说明、评测、招聘信息。

---

## 输出：交互式 HTML 竞品手册

本技能生成一个**独立 HTML 文件**，包含：

### 1. 对比矩阵（落地视图）
一览对比您与所有竞争对手：
- 功能对比网格
- 定价对比
- 市场定位
- 赢率指标（如已连接 CRM）

### 2. 竞品标签页（点击展开）
每个竞品有一个可点击的卡片，展开后显示：
- 公司简介（规模、融资、目标市场）
- 所售产品及定位
- 近期发布（90 天内）
- 他们赢在哪里 vs. 您赢在哪里
- 定价情报
- 针对不同场景的话术
- 异议处理
- 埋雷问题

### 3. 我方公司卡片
- 我方近期发布（90 天内）
- 我方核心差异化优势
- 有力案例和客户引语

---

## HTML 结构

```html
<!DOCTYPE html>
<html>
<head>
    <title>Battlecard: [Your Company] vs Competitors</title>
    <style>
        /* Dark theme, professional styling */
        /* Tabbed navigation */
        /* Expandable cards */
        /* Responsive design */
    </style>
</head>
<body>
    <!-- Header with your company + date -->
    <header>
        <h1>[Your Company] Competitive Battlecard</h1>
        <p>Generated: [Date] | Competitors: [List]</p>
    </header>

    <!-- Tab Navigation -->
    <nav class="tabs">
        <button class="tab active" data-tab="matrix">Comparison Matrix</button>
        <button class="tab" data-tab="competitor-1">[Competitor 1]</button>
        <button class="tab" data-tab="competitor-2">[Competitor 2]</button>
        <button class="tab" data-tab="competitor-3">[Competitor 3]</button>
    </nav>

    <!-- Comparison Matrix Tab -->
    <section id="matrix" class="tab-content active">
        <h2>Head-to-Head Comparison</h2>
        <table class="comparison-matrix">
            <!-- Feature rows with you vs each competitor -->
        </table>

        <h2>Quick Win/Loss Guide</h2>
        <div class="win-loss-grid">
            <!-- Per-competitor: when you win, when you lose -->
        </div>
    </section>

    <!-- Individual Competitor Tabs -->
    <section id="competitor-1" class="tab-content">
        <div class="battlecard">
            <div class="profile"><!-- Company info --></div>
            <div class="differentiation"><!-- Where they win / you win --></div>
            <div class="talk-tracks"><!-- Scenario-based positioning --></div>
            <div class="objections"><!-- Common objections + responses --></div>
            <div class="landmines"><!-- Questions to ask --></div>
        </div>
    </section>

    <script>
        // Tab switching logic
        // Expand/collapse sections
    </script>
</body>
</html>
```

---

## 视觉设计

### 配色系统
```css
:root {
    /* Dark theme base */
    --bg-primary: #0a0d14;
    --bg-elevated: #0f131c;
    --bg-surface: #161b28;
    --bg-hover: #1e2536;

    /* Text */
    --text-primary: #ffffff;
    --text-secondary: rgba(255, 255, 255, 0.7);
    --text-muted: rgba(255, 255, 255, 0.5);

    /* Accent (your brand or neutral) */
    --accent: #3b82f6;
    --accent-hover: #2563eb;

    /* Status indicators */
    --you-win: #10b981;
    --they-win: #ef4444;
    --tie: #f59e0b;
}
```

### 卡片设计
- 圆角（12px）
- 细边框（1px，低透明度）
- 鼠标悬停时轻微浮起
- 流畅过渡（200ms）

### 对比矩阵
- 固定表头行
- 颜色编码的获胜标记（绿色=您，红色=对手，黄色=平局）
- 可展开的行以查看详情

---

## 执行流程

### 阶段一：获取销售方背景

```
首次使用：
1. 询问："您在哪家公司工作？"
2. 询问："您卖什么？（一句话描述产品/服务）"
3. 询问："主要竞争对手有哪些？（最多5个）"
4. 存储背景供后续会话使用

再次使用：
1. 确认："还是在 [公司] 卖 [产品]？"
2. 询问："竞争对手不变，还是有新增？"
```

### 阶段二：调研我方公司（始终执行）

```
网络搜索：
1. "[Your company] product" — 当前产品
2. "[Your company] pricing" — 定价模式
3. "[Your company] news" — 近期公告（90 天）
4. "[Your company] product updates OR changelog OR releases" — 已发布内容
5. "[Your company] vs [competitor]" — 现有对比分析
```

### 阶段三：调研每个竞争对手（始终执行）

```
针对每个竞争对手，执行：
1. "[Competitor] product features" — 产品功能
2. "[Competitor] pricing" — 定价方式
3. "[Competitor] news" — 近期公告
4. "[Competitor] product updates OR changelog OR releases" — 已发布内容
5. "[Competitor] reviews G2 OR Capterra OR TrustRadius" — 客户评价
6. "[Competitor] vs [alternatives]" — 市场定位
7. "[Competitor] customers" — 谁在使用
8. "[Competitor] careers" — 招聘信号（增长领域）
```

### 阶段四：提取已连接来源（如可用）

```
如果已连接 CRM：
1. 查询竞争对手字段 = [竞品] 的已结束赢单
2. 查询竞争对手字段 = [竞品] 的已结束输单
3. 提取赢/输规律

如果已连接文档：
1. 搜索 "battlecard [competitor]"
2. 搜索 "competitive [competitor]"
3. 提取现有定位文档

如果已连接即时通讯：
1. 搜索过去 90 天的 "[竞品]" 提及
2. 提取一线情报和同事见解

如果已连接转录：
1. 搜索通话中的 "[竞品]" 提及
2. 提取异议和客户引语
```

### 阶段五：构建 HTML 工件

```
1. 整理每个竞争对手的数据
2. 构建对比矩阵
3. 生成各竞品手册
4. 为每个场景创建销售话术
5. 汇总埋雷问题
6. 渲染为独立 HTML
7. 保存为 [YourCompany]-battlecard-[date].html
```

---

## 竞品数据结构

```yaml
competitor:
  name: "[Name]"
  website: "[URL]"
  profile:
    founded: "[Year]"
    funding: "[Stage + amount]"
    employees: "[Count]"
    target_market: "[Who they sell to]"
    pricing_model: "[Per seat / usage / etc.]"
    market_position: "[Leader / Challenger / Niche]"

  what_they_sell: "[Product summary]"
  their_positioning: "[How they describe themselves]"

  recent_releases:
    - date: "[Date]"
      release: "[Feature/Product]"
      impact: "[Why it matters]"

  where_they_win:
    - area: "[Area]"
      advantage: "[Their strength]"
      how_to_handle: "[Your counter]"

  where_you_win:
    - area: "[Area]"
      advantage: "[Your strength]"
      proof_point: "[Evidence]"

  pricing:
    model: "[How they charge]"
    entry_price: "[Starting price]"
    enterprise: "[Enterprise pricing]"
    hidden_costs: "[Implementation, etc.]"
    talk_track: "[How to discuss pricing]"

  talk_tracks:
    early_mention: "[Strategy if they come up early]"
    displacement: "[Strategy if customer uses them]"
    late_addition: "[Strategy if added late to eval]"

  objections:
    - objection: "[What customer says]"
      response: "[How to handle]"

  landmines:
    - "[Question that exposes their weakness]"

  win_loss: # If CRM connected
    win_rate: "[X]%"
    common_win_factors: "[What predicts wins]"
    common_loss_factors: "[What predicts losses]"
```

---

## 交付说明

```markdown
## ✓ Battlecard Created

[View your battlecard](file:///path/to/[YourCompany]-battlecard-[date].html)

---

**Summary**
- **Your Company**: [Name]
- **Competitors Analyzed**: [List]
- **Data Sources**: Web research [+ CRM] [+ Docs] [+ Transcripts]

---

**How to Use**
- **Before a call**: Open the relevant competitor tab, review talk tracks
- **During a call**: Reference landmine questions
- **After win/loss**: Update with new intel

---

**Sharing Options**
- **Local file**: Open in any browser
- **Host it**: Upload to Netlify, Vercel, or internal wiki
- **Share directly**: Send the HTML file to teammates

---

**Keep it Fresh**
Run this skill again to refresh with latest intel. Recommended: monthly or before major deals.
```

---

## 刷新频率

竞情数据会过时。建议刷新周期：

| 触发条件 | 操作 |
|---------|------|
| **每月** | 快速刷新——新发布、新闻、定价变动 |
| **重要交易前** | 针对该交易中竞争对手的深度刷新 |
| **赢/输后** | 用新数据更新规律 |
| **竞品公告** | 立即更新该竞争对手信息 |

---

## 获得更好情报的建议

1. **诚实面对自身弱点** — 承认竞争对手的强项才能建立可信度
2. **关注结果而非功能** — "他们有 X 功能"不如"客户用它实现了 Y 结果"重要
3. **从一线更新** — 最好的情报来自实际客户对话，而非网站
4. **埋雷而非贬低** — 提出能暴露弱点的问题；永远不要恶意抨击
5. **持续跟踪发布** — 竞品发布内容揭示了他们的战略和您的机会

---

## 相关技能

- **account-research** — 外联前调研特定潜在客户
- **call-prep** — 为涉及已知竞争对手的通话做准备
- **create-an-asset** — 为特定交易构建定制对比页面
