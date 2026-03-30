---
name: data-visualization
description: 使用 Python（matplotlib、seaborn、plotly）创建有效的数据可视化。用于构建图表、为数据集选择合适的图表类型、创建出版级图形，或应用无障碍访问和色彩理论等设计原则时触发。触发词：数据可视化、图表选择
user-invocable: false
---

# 数据可视化技能

图表选择指南、Python 可视化代码模式、设计原则和无障碍访问注意事项，帮助创建有效的数据可视化。

## 图表选择指南

### 按数据关系选择

| 要展示的内容 | 最佳图表 | 备选方案 |
|---|---|---|
| **随时间的趋势** | 折线图 | 面积图（展示累计或构成时） |
| **类别间的对比** | 垂直柱状图 | 水平柱状图（类别较多时），哑铃图 |
| **排名** | 水平柱状图 | 点图，斜率图（对比两个时期） |
| **整体中的部分构成** | 堆叠柱状图 | 树图（层级数据），华夫图 |
| **随时间的构成变化** | 堆叠面积图 | 100% 堆叠柱状图（聚焦比例时） |
| **值的分布** | 直方图 | 箱形图（对比多组），小提琴图，条形图 |
| **两变量的相关性** | 散点图 | 气泡图（添加第三变量作为大小） |
| **多变量相关性** | 热力图（相关矩阵） | 配对图 |
| **地理规律** | 分级统计图 | 气泡地图，蜂巢图 |
| **流向/过程** | 桑基图 | 漏斗图（顺序阶段） |
| **关系网络** | 网络图 | 弦图 |
| **实际值 vs 目标值** | 子弹图 | 仪表盘（仅单个 KPI） |
| **多个 KPI 同时展示** | 小多图 | 分离图表的仪表板 |

### 某些图表的禁用场景

- **饼图**：避免使用，除非类别少于6个且精确比例不那么重要。人类不擅长比较角度。改用柱状图。
- **三维图表**：绝对不用。它们扭曲视觉感知，且不增加任何信息量。
- **双轴图表**：谨慎使用。可能通过暗示相关性产生误导。如使用，请清晰标注两个轴。
- **多类别堆叠柱状图**：难以比较中间段。改用小多图或分组柱状图。
- **圆环图**：略优于饼图，但存在同样的根本问题。最多用于单个 KPI 展示。

## Python 可视化代码模式

### 环境配置与样式

```python
import matplotlib.pyplot as plt
import matplotlib.ticker as mticker
import seaborn as sns
import pandas as pd
import numpy as np

# 专业样式配置
plt.style.use('seaborn-v0_8-whitegrid')
plt.rcParams.update({
    'figure.figsize': (10, 6),
    'figure.dpi': 150,
    'font.size': 11,
    'axes.titlesize': 14,
    'axes.titleweight': 'bold',
    'axes.labelsize': 11,
    'xtick.labelsize': 10,
    'ytick.labelsize': 10,
    'legend.fontsize': 10,
    'figure.titlesize': 16,
})

# 色盲友好调色板
PALETTE_CATEGORICAL = ['#4C72B0', '#DD8452', '#55A868', '#C44E52', '#8172B3', '#937860']
PALETTE_SEQUENTIAL = 'YlOrRd'
PALETTE_DIVERGING = 'RdBu_r'
```

### 折线图（时间序列）

```python
fig, ax = plt.subplots(figsize=(10, 6))

for label, group in df.groupby('category'):
    ax.plot(group['date'], group['value'], label=label, linewidth=2)

ax.set_title('各类别指标趋势', fontweight='bold')
ax.set_xlabel('日期')
ax.set_ylabel('值')
ax.legend(loc='upper left', frameon=True)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# 格式化 x 轴日期
fig.autofmt_xdate()

plt.tight_layout()
plt.savefig('trend_chart.png', dpi=150, bbox_inches='tight')
```

### 柱状图（对比）

```python
fig, ax = plt.subplots(figsize=(10, 6))

# 按值排序，便于阅读
df_sorted = df.sort_values('metric', ascending=True)

bars = ax.barh(df_sorted['category'], df_sorted['metric'], color=PALETTE_CATEGORICAL[0])

# 添加数值标签
for bar in bars:
    width = bar.get_width()
    ax.text(width + 0.5, bar.get_y() + bar.get_height()/2,
            f'{width:,.0f}', ha='left', va='center', fontsize=10)

ax.set_title('各类别指标（排序）', fontweight='bold')
ax.set_xlabel('指标值')
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

plt.tight_layout()
plt.savefig('bar_chart.png', dpi=150, bbox_inches='tight')
```

### 直方图（分布）

```python
fig, ax = plt.subplots(figsize=(10, 6))

ax.hist(df['value'], bins=30, color=PALETTE_CATEGORICAL[0], edgecolor='white', alpha=0.8)

# 添加均值和中位数线
mean_val = df['value'].mean()
median_val = df['value'].median()
ax.axvline(mean_val, color='red', linestyle='--', linewidth=1.5, label=f'均值: {mean_val:,.1f}')
ax.axvline(median_val, color='green', linestyle='--', linewidth=1.5, label=f'中位数: {median_val:,.1f}')

ax.set_title('值分布', fontweight='bold')
ax.set_xlabel('值')
ax.set_ylabel('频率')
ax.legend()
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

plt.tight_layout()
plt.savefig('histogram.png', dpi=150, bbox_inches='tight')
```

### 热力图

```python
fig, ax = plt.subplots(figsize=(10, 8))

# 将数据透视为热力图格式
pivot = df.pivot_table(index='row_dim', columns='col_dim', values='metric', aggfunc='sum')

sns.heatmap(pivot, annot=True, fmt=',.0f', cmap='YlOrRd',
            linewidths=0.5, ax=ax, cbar_kws={'label': '指标值'})

ax.set_title('行维度与列维度的指标分布', fontweight='bold')
ax.set_xlabel('列维度')
ax.set_ylabel('行维度')

plt.tight_layout()
plt.savefig('heatmap.png', dpi=150, bbox_inches='tight')
```

### 小多图

```python
categories = df['category'].unique()
n_cats = len(categories)
n_cols = min(3, n_cats)
n_rows = (n_cats + n_cols - 1) // n_cols

fig, axes = plt.subplots(n_rows, n_cols, figsize=(5*n_cols, 4*n_rows), sharex=True, sharey=True)
axes = axes.flatten() if n_cats > 1 else [axes]

for i, cat in enumerate(categories):
    ax = axes[i]
    subset = df[df['category'] == cat]
    ax.plot(subset['date'], subset['value'], color=PALETTE_CATEGORICAL[i % len(PALETTE_CATEGORICAL)])
    ax.set_title(cat, fontsize=12)
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)

# 隐藏空子图
for j in range(i+1, len(axes)):
    axes[j].set_visible(False)

fig.suptitle('各类别趋势', fontsize=14, fontweight='bold', y=1.02)
plt.tight_layout()
plt.savefig('small_multiples.png', dpi=150, bbox_inches='tight')
```

### 数字格式化辅助函数

```python
def format_number(val, format_type='number'):
    """格式化图表标签数字。"""
    if format_type == 'currency':
        if abs(val) >= 1e9:
            return f'${val/1e9:.1f}B'
        elif abs(val) >= 1e6:
            return f'${val/1e6:.1f}M'
        elif abs(val) >= 1e3:
            return f'${val/1e3:.1f}K'
        else:
            return f'${val:,.0f}'
    elif format_type == 'percent':
        return f'{val:.1f}%'
    elif format_type == 'number':
        if abs(val) >= 1e9:
            return f'{val/1e9:.1f}B'
        elif abs(val) >= 1e6:
            return f'{val/1e6:.1f}M'
        elif abs(val) >= 1e3:
            return f'{val/1e3:.1f}K'
        else:
            return f'{val:,.0f}'
    return str(val)

# 用于坐标轴格式化
ax.yaxis.set_major_formatter(mticker.FuncFormatter(lambda x, p: format_number(x, 'currency')))
```

### 使用 Plotly 创建交互式图表

```python
import plotly.express as px
import plotly.graph_objects as go

# 简单的交互式折线图
fig = px.line(df, x='date', y='value', color='category',
              title='交互式指标趋势',
              labels={'value': '指标值', 'date': '日期'})
fig.update_layout(hovermode='x unified')
fig.write_html('interactive_chart.html')
fig.show()

# 带悬停数据的交互式散点图
fig = px.scatter(df, x='metric_a', y='metric_b', color='category',
                 size='size_metric', hover_data=['name', 'detail_field'],
                 title='相关性分析')
fig.show()
```

## 设计原则

### 颜色

- **有目的地使用颜色**：颜色应编码数据，而非装饰
- **突出核心信息**：对关键洞察使用醒目的强调色；其余使用灰色
- **有序数据**：使用单色渐变（浅到深）表示有序值
- **发散数据**：使用以中性色为中点的双色渐变，适用于有意义中心点的数据
- **分类数据**：使用不同色调，超过 6-8 个类别会变得混乱
- **避免仅用红/绿**：8% 的男性存在红绿色盲。优先使用蓝色/橙色作为主色对

### 文字

- **标题陈述洞察**："营收同比增长 23%" 优于 "按月营收"
- **副标题添加背景**：时间范围、应用的过滤器、数据来源
- **坐标轴标签可读**：尽量避免 90 度旋转。改用缩短或换行
- **数据标签增加精确度**：标注关键点，而非每一根柱子
- **标注突出显示**：用文字标注特定关注点

### 布局

- **减少图表噪音**：移除不承载信息的网格线、边框和背景
- **有意义地排序**：按值排序类别（而非字母序），除非有自然顺序（月份、阶段）
- **合适的宽高比**：时间序列宜宽（3:1 至 2:1）；对比图可以更接近正方形
- **空白是好东西**：不要把图表塞得满满当当。每个可视化都需要呼吸的空间

### 准确性

- **柱状图从零开始**：始终如此。从 95 到 100 的柱子会夸大 5% 的差异
- **折线图可以有非零基线**：当变化范围本身有意义时
- **跨图表保持一致的刻度**：对比多个图表时，使用相同的轴范围
- **展示不确定性**：数据不确定时，使用误差棒、置信区间或范围
- **标注坐标轴**：绝不让读者猜测数字的含义

## 无障碍访问注意事项

### 色觉障碍

- 绝不仅靠颜色区分数据系列
- 添加填充图案、不同线型（实线、虚线、点线），或直接标注
- 使用色觉障碍模拟器测试（如 Coblis、Sim Daltonism）
- 使用色盲友好调色板：`sns.color_palette("colorblind")`

### 屏幕阅读器

- 包含描述图表核心发现的替代文本
- 在可视化旁提供数据表格版本
- 使用语义化的标题和标签

### 通用无障碍访问

- 数据元素与背景之间保持足够的对比度
- 标签文字最小 10 磅，标题最小 12 磅
- 避免仅通过空间位置传递信息（添加标签）
- 考虑打印效果：图表在黑白打印时是否仍然可读？

### 无障碍访问检查清单

分享可视化前：
- [ ] 图表在去掉颜色的情况下仍然可用（用图案、标签或线型区分系列）
- [ ] 标准缩放级别下文字可读
- [ ] 标题描述洞察而非仅描述数据
- [ ] 坐标轴已标注单位
- [ ] 图例清晰且位置不遮挡数据
- [ ] 已注明数据来源和时间范围
