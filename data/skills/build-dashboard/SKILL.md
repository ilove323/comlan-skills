---
name: build-dashboard
description: 构建带图表、过滤器和表格的交互式 HTML 仪表板。用于创建含 KPI 卡片的高管概览、将查询结果转化为可分享的自包含报告、构建团队监控快照，或需要在单个可在浏览器中打开的文件中展示多个带过滤功能的图表时触发。触发词：创建仪表板、dashboard
---

# /build-dashboard - 构建交互式仪表板

> 如遇到不熟悉的占位符或需要查看哪些工具已接入，请参阅 [CONNECTORS.md](../../CONNECTORS.md)。

构建自包含的交互式 HTML 仪表板，包含图表、过滤器、表格和专业样式。可直接在浏览器中打开——无需服务器或额外依赖。

## 使用方式

```
/build-dashboard <仪表板描述> [数据来源]
```

## 工作流

### 1. 理解仪表板需求

确定：

- **用途**：高管概览、运营监控、深度分析、团队汇报
- **受众**：谁会使用这个仪表板？
- **核心指标**：哪些数字最重要？
- **维度**：用户应该能按什么过滤或切分数据？
- **数据来源**：实时查询、粘贴的数据、CSV 文件，或示例数据

### 2. 收集数据

**如果已接入数据仓库：**
1. 查询所需数据
2. 将结果以 JSON 形式嵌入 HTML 文件

**如果数据是粘贴或上传的：**
1. 解析并清洗数据
2. 以 JSON 形式嵌入仪表板

**如果根据描述工作但没有实际数据：**
1. 创建符合所描述 Schema 的真实感样本数据集
2. 在仪表板中注明使用的是样本数据
3. 提供替换为真实数据的说明

### 3. 设计仪表板布局

遵循标准仪表板布局模式：

```
┌──────────────────────────────────────────────────┐
│  仪表板标题                        [过滤器 ▼]    │
├────────────┬────────────┬────────────┬───────────┤
│  KPI 卡片  │  KPI 卡片  │  KPI 卡片  │ KPI 卡片  │
├────────────┴────────────┼────────────┴───────────┤
│                         │                        │
│    主图表               │   次要图表              │
│    （最大区域）          │                        │
│                         │                        │
├─────────────────────────┴────────────────────────┤
│                                                  │
│    详情表格（可排序、可滚动）                      │
│                                                  │
└──────────────────────────────────────────────────┘
```

**根据内容调整布局：**
- 顶部放 2-4 个 KPI 卡片，展示核心数字
- 中间区域放 1-3 个图表，展示趋势和细分
- 底部可选展示详情表格，供深入查看
- 根据复杂度，将过滤器放在页眉或侧边栏

### 4. 构建 HTML 仪表板

使用下方的基础模板，生成单个自包含 HTML 文件，包含：

**结构（HTML）：**
- 语义化 HTML5 布局
- 使用 CSS Grid 或 Flexbox 的响应式网格
- 过滤控件（下拉菜单、日期选择器、切换按钮）
- 带数值和标签的 KPI 卡片
- 图表容器
- 带可排序表头的数据表格

**样式（CSS）：**
- 专业配色方案（干净的白色、灰色，带数据强调色）
- 带柔和阴影的卡片式布局
- 一致的字体排版（使用系统字体，加载速度快）
- 适配不同屏幕尺寸的响应式设计
- 打印友好样式

**交互性（JavaScript）：**
- Chart.js 用于交互式图表（通过 CDN 引入）
- 下拉过滤器同时更新所有图表和表格
- 可排序的表格列
- 图表的悬停提示
- 数字格式化（千位分隔符、货币、百分比）

**数据（嵌入 JSON）：**
- 所有数据以 JavaScript 变量的形式直接嵌入 HTML
- 无需外部数据请求
- 仪表板完全可离线使用

### 5. 实现图表类型

所有图表使用 Chart.js。常见仪表板图表模式：

- **折线图**：时间序列趋势
- **柱状图**：类别对比
- **圆环图**：构成（类别少于6个时）
- **堆叠柱状图**：随时间的构成变化
- **混合图（柱状图 + 折线图）**：数量与比率叠加

### 6. 添加交互性

使用下方的过滤器和交互性实现模式，实现下拉过滤、日期范围过滤、组合过滤逻辑、可排序表格和图表更新。

### 7. 保存并打开

1. 将仪表板保存为带描述性名称的 HTML 文件（如 `sales_dashboard.html`）
2. 在用户的默认浏览器中打开
3. 确认正确渲染
4. 提供更新数据或自定义的说明

---

## 基础模板

每个仪表板遵循以下结构：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Title</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.5.1" integrity="sha384-jb8JQMbMoBUzgWatfe6COACi2ljcDdZQ2OxczGA3bGNeWe+6DChMTBJemed7ZnvJ" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-date-fns@3.0.0" integrity="sha384-cVMg8E3QFwTvGCDuK+ET4PD341jF3W8nO1auiXfuZNQkzbUUiBGLsIQUE+b1mxws" crossorigin="anonymous"></script>
    <style>
        /* 仪表板样式放这里 */
    </style>
</head>
<body>
    <div class="dashboard-container">
        <header class="dashboard-header">
            <h1>Dashboard Title</h1>
            <div class="filters">
                <!-- 过滤控件 -->
            </div>
        </header>

        <section class="kpi-row">
            <!-- KPI 卡片 -->
        </section>

        <section class="chart-row">
            <!-- 图表容器 -->
        </section>

        <section class="table-section">
            <!-- 数据表格 -->
        </section>

        <footer class="dashboard-footer">
            <span>数据截至：<span id="data-date"></span></span>
        </footer>
    </div>

    <script>
        // 嵌入数据
        const DATA = [];

        // 仪表板逻辑
        class Dashboard {
            constructor(data) {
                this.rawData = data;
                this.filteredData = data;
                this.charts = {};
                this.init();
            }

            init() {
                this.setupFilters();
                this.renderKPIs();
                this.renderCharts();
                this.renderTable();
            }

            applyFilters() {
                // 过滤逻辑
                this.filteredData = this.rawData.filter(row => {
                    // 应用每个激活的过滤器
                    return true; // 占位符
                });
                this.renderKPIs();
                this.updateCharts();
                this.renderTable();
            }

            // ... 每个区块的方法
        }

        const dashboard = new Dashboard(DATA);
    </script>
</body>
</html>
```

## KPI 卡片模式

```html
<div class="kpi-card">
    <div class="kpi-label">Total Revenue</div>
    <div class="kpi-value" id="kpi-revenue">$0</div>
    <div class="kpi-change positive" id="kpi-revenue-change">+0%</div>
</div>
```

```javascript
function renderKPI(elementId, value, previousValue, format = 'number') {
    const el = document.getElementById(elementId);
    const changeEl = document.getElementById(elementId + '-change');

    // 格式化数值
    el.textContent = formatValue(value, format);

    // 计算并显示变化
    if (previousValue && previousValue !== 0) {
        const pctChange = ((value - previousValue) / previousValue) * 100;
        const sign = pctChange >= 0 ? '+' : '';
        changeEl.textContent = `${sign}${pctChange.toFixed(1)}% vs prior period`;
        changeEl.className = `kpi-change ${pctChange >= 0 ? 'positive' : 'negative'}`;
    }
}

function formatValue(value, format) {
    switch (format) {
        case 'currency':
            if (value >= 1e6) return `$${(value / 1e6).toFixed(1)}M`;
            if (value >= 1e3) return `$${(value / 1e3).toFixed(1)}K`;
            return `$${value.toFixed(0)}`;
        case 'percent':
            return `${value.toFixed(1)}%`;
        case 'number':
            if (value >= 1e6) return `${(value / 1e6).toFixed(1)}M`;
            if (value >= 1e3) return `${(value / 1e3).toFixed(1)}K`;
            return value.toLocaleString();
        default:
            return value.toString();
    }
}
```

## Chart.js 集成

### 图表容器模式

```html
<div class="chart-container">
    <h3 class="chart-title">月度营收趋势</h3>
    <canvas id="revenue-chart"></canvas>
</div>
```

### 折线图

```javascript
function createLineChart(canvasId, labels, datasets) {
    const ctx = document.getElementById(canvasId).getContext('2d');
    return new Chart(ctx, {
        type: 'line',
        data: {
            labels: labels,
            datasets: datasets.map((ds, i) => ({
                label: ds.label,
                data: ds.data,
                borderColor: COLORS[i % COLORS.length],
                backgroundColor: COLORS[i % COLORS.length] + '20',
                borderWidth: 2,
                fill: ds.fill || false,
                tension: 0.3,
                pointRadius: 3,
                pointHoverRadius: 6,
            }))
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            interaction: {
                mode: 'index',
                intersect: false,
            },
            plugins: {
                legend: {
                    position: 'top',
                    labels: { usePointStyle: true, padding: 20 }
                },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            return `${context.dataset.label}: ${formatValue(context.parsed.y, 'currency')}`;
                        }
                    }
                }
            },
            scales: {
                x: {
                    grid: { display: false }
                },
                y: {
                    beginAtZero: true,
                    ticks: {
                        callback: function(value) {
                            return formatValue(value, 'currency');
                        }
                    }
                }
            }
        }
    });
}
```

### 柱状图

```javascript
function createBarChart(canvasId, labels, data, options = {}) {
    const ctx = document.getElementById(canvasId).getContext('2d');
    const isHorizontal = options.horizontal || labels.length > 8;

    return new Chart(ctx, {
        type: 'bar',
        data: {
            labels: labels,
            datasets: [{
                label: options.label || 'Value',
                data: data,
                backgroundColor: options.colors || COLORS.map(c => c + 'CC'),
                borderColor: options.colors || COLORS,
                borderWidth: 1,
                borderRadius: 4,
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            indexAxis: isHorizontal ? 'y' : 'x',
            plugins: {
                legend: { display: false },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            return formatValue(context.parsed[isHorizontal ? 'x' : 'y'], options.format || 'number');
                        }
                    }
                }
            },
            scales: {
                x: {
                    beginAtZero: true,
                    grid: { display: isHorizontal },
                    ticks: isHorizontal ? {
                        callback: function(value) {
                            return formatValue(value, options.format || 'number');
                        }
                    } : {}
                },
                y: {
                    beginAtZero: !isHorizontal,
                    grid: { display: !isHorizontal },
                    ticks: !isHorizontal ? {
                        callback: function(value) {
                            return formatValue(value, options.format || 'number');
                        }
                    } : {}
                }
            }
        }
    });
}
```

### 圆环图

```javascript
function createDoughnutChart(canvasId, labels, data) {
    const ctx = document.getElementById(canvasId).getContext('2d');
    return new Chart(ctx, {
        type: 'doughnut',
        data: {
            labels: labels,
            datasets: [{
                data: data,
                backgroundColor: COLORS.map(c => c + 'CC'),
                borderColor: '#ffffff',
                borderWidth: 2,
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            cutout: '60%',
            plugins: {
                legend: {
                    position: 'right',
                    labels: { usePointStyle: true, padding: 15 }
                },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            const total = context.dataset.data.reduce((a, b) => a + b, 0);
                            const pct = ((context.parsed / total) * 100).toFixed(1);
                            return `${context.label}: ${formatValue(context.parsed, 'number')} (${pct}%)`;
                        }
                    }
                }
            }
        }
    });
}
```

### 过滤器变更时更新图表

```javascript
function updateChart(chart, newLabels, newData) {
    chart.data.labels = newLabels;

    if (Array.isArray(newData[0])) {
        // 多数据集
        newData.forEach((data, i) => {
            chart.data.datasets[i].data = data;
        });
    } else {
        chart.data.datasets[0].data = newData;
    }

    chart.update('none'); // 'none' 禁用动画，实现即时更新
}
```

## 过滤器与交互性实现

### 下拉过滤器

```html
<div class="filter-group">
    <label for="filter-region">地区</label>
    <select id="filter-region" onchange="dashboard.applyFilters()">
        <option value="all">全部地区</option>
    </select>
</div>
```

```javascript
function populateFilter(selectId, data, field) {
    const select = document.getElementById(selectId);
    const values = [...new Set(data.map(d => d[field]))].sort();

    // 保留"全部"选项，添加唯一值
    values.forEach(val => {
        const option = document.createElement('option');
        option.value = val;
        option.textContent = val;
        select.appendChild(option);
    });
}

function getFilterValue(selectId) {
    const val = document.getElementById(selectId).value;
    return val === 'all' ? null : val;
}
```

### 日期范围过滤器

```html
<div class="filter-group">
    <label>日期范围</label>
    <input type="date" id="filter-date-start" onchange="dashboard.applyFilters()">
    <span>至</span>
    <input type="date" id="filter-date-end" onchange="dashboard.applyFilters()">
</div>
```

```javascript
function filterByDateRange(data, dateField, startDate, endDate) {
    return data.filter(row => {
        const rowDate = new Date(row[dateField]);
        if (startDate && rowDate < new Date(startDate)) return false;
        if (endDate && rowDate > new Date(endDate)) return false;
        return true;
    });
}
```

### 组合过滤逻辑

```javascript
applyFilters() {
    const region = getFilterValue('filter-region');
    const category = getFilterValue('filter-category');
    const startDate = document.getElementById('filter-date-start').value;
    const endDate = document.getElementById('filter-date-end').value;

    this.filteredData = this.rawData.filter(row => {
        if (region && row.region !== region) return false;
        if (category && row.category !== category) return false;
        if (startDate && row.date < startDate) return false;
        if (endDate && row.date > endDate) return false;
        return true;
    });

    this.renderKPIs();
    this.updateCharts();
    this.renderTable();
}
```

### 可排序表格

```javascript
function renderTable(containerId, data, columns) {
    const container = document.getElementById(containerId);
    let sortCol = null;
    let sortDir = 'desc';

    function render(sortedData) {
        let html = '<table class="data-table">';

        // 表头
        html += '<thead><tr>';
        columns.forEach(col => {
            const arrow = sortCol === col.field
                ? (sortDir === 'asc' ? ' ▲' : ' ▼')
                : '';
            html += `<th onclick="sortTable('${col.field}')" style="cursor:pointer">${col.label}${arrow}</th>`;
        });
        html += '</tr></thead>';

        // 表体
        html += '<tbody>';
        sortedData.forEach(row => {
            html += '<tr>';
            columns.forEach(col => {
                const value = col.format ? formatValue(row[col.field], col.format) : row[col.field];
                html += `<td>${value}</td>`;
            });
            html += '</tr>';
        });
        html += '</tbody></table>';

        container.innerHTML = html;
    }

    window.sortTable = function(field) {
        if (sortCol === field) {
            sortDir = sortDir === 'asc' ? 'desc' : 'asc';
        } else {
            sortCol = field;
            sortDir = 'desc';
        }
        const sorted = [...data].sort((a, b) => {
            const aVal = a[field], bVal = b[field];
            const cmp = aVal < bVal ? -1 : aVal > bVal ? 1 : 0;
            return sortDir === 'asc' ? cmp : -cmp;
        });
        render(sorted);
    };

    render(data);
}
```

## 大数据集的性能注意事项

### 数据量指南

| 数据量 | 方案 |
|---|---|
| <1,000 行 | 直接嵌入 HTML。完整交互性。 |
| 1,000 - 10,000 行 | 嵌入 HTML。图表可能需要预聚合。 |
| 10,000 - 100,000 行 | 服务端预聚合。仅嵌入聚合后数据。 |
| >100,000 行 | 不适合客户端仪表板。使用 BI 工具或分页。 |

### 预聚合模式

不要嵌入原始数据后在浏览器中聚合，而是：

```javascript
// 不要这样：嵌入 50,000 行原始数据
const RAW_DATA = [/* 50,000 行 */];

// 应该这样：嵌入前先预聚合
const CHART_DATA = {
    monthly_revenue: [
        { month: '2024-01', revenue: 150000, orders: 1200 },
        { month: '2024-02', revenue: 165000, orders: 1350 },
        // ... 12 行而非 50,000 行
    ],
    top_products: [
        { product: 'Widget A', revenue: 45000 },
        // ... 10 行
    ],
    kpis: {
        total_revenue: 1980000,
        total_orders: 15600,
        avg_order_value: 127,
    }
};
```

## 示例

```
/build-dashboard 月度销售仪表板，展示营收趋势、热门产品和地区分布。数据在 orders 表中。
```

```
/build-dashboard 这是我们的支持工单数据 [粘贴 CSV]。构建一个仪表板，展示按优先级的工单量、响应时间趋势和解决率。
```

```
/build-dashboard 为 SaaS 公司创建一个高管仪表板模板，展示 MRR、流失率、新客户和 NPS。使用示例数据。
```

## 提示

- 仪表板是完全自包含的 HTML 文件——发送文件给任何人即可分享
- 如需实时仪表板，考虑接入 BI 工具。这些仪表板是某一时刻的快照
- 可以请求"深色模式"或"演示模式"以获得不同样式
- 可以指定特定的配色方案以匹配你的品牌
