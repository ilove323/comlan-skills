---
name: view-pdf
description: 交互式PDF查看器。当用户想要打开、显示或查看PDF并进行可视化协作时使用——包括注释、高亮、盖章、填写表单字段、放置签名/首字母缩写或共同审阅标记。不适用于摘要或文本提取（请改用原生Read工具）。触发词：查看PDF、PDF预览、view-pdf
---

# PDF查看器——交互式文档工作流

你可以访问本地PDF服务器，它能在实时查看器中渲染文档，并支持注释、填写表单和放置签名，具有实时视觉反馈。

## 何时使用此技能

**当用户需要交互功能时使用PDF查看器：**
- "给我看这份合同" / "打开这篇论文"
- "高亮关键条款让我审阅"
- "帮我填写这份表单"
- "在第3页签名" / "在每一页加上我的首字母缩写"
- "盖上CONFIDENTIAL印章" / "标记为已批准"
- "带我浏览这份文件并注释重要部分"

**以下情况请勿使用查看器：**
- "总结这份PDF" → 直接使用原生Read工具
- "第5页写了什么？" → 使用Read
- "提取第3节的表格" → 使用Read

查看器的价值在于向用户展示文档并协作标记——而非向你传输文本流。

## 工具

### `list_pdfs`
列出可用的本地PDF及允许的本地目录。无需参数。

### `display_pdf`
在交互式查看器中打开PDF。**每个文档只调用一次。**
- `url` — 本地文件路径或HTTPS URL
- `page` — 初始页码（可选，默认为1）
- `elicit_form_inputs` — 若为`true`，则在显示前提示用户填写表单字段（用于交互式表单填写）

返回`viewUUID`——将其传递给每个`interact`调用。再次调用`display_pdf`会创建**独立**查看器；使用新UUID的interact调用不会影响用户正在查看的那个。

若PDF包含可填写字段，还会返回`formFields`（名称、类型、页码、边界框）——可用于签名位置确定。

### `interact`
`display_pdf`之后的所有后续操作。传入`viewUUID`加上一个或多个命令。通过`commands`数组**在一次调用中批量执行多个命令**——按顺序运行。每批操作结束时加上`get_screenshot`以可视化验证更改。

**注释操作：**
- `add_annotations` — 添加标记（见下方类型）
- `update_annotations` — 修改已有标记（需要id和type）
- `remove_annotations` — 按id数组删除
- `highlight_text` — 自动查找文本并高亮（比手动指定rects更适合文本标记）

**导航操作：**
- `navigate`（页码）、`search`（查询词）、`find`（查询词，静默）、
  `search_navigate`（匹配索引）、`zoom`（缩放比例0.5–3.0）

**提取操作：**
- `get_text` — 从页面范围提取文本（最多20页）。用于读取内容以决定注释内容，不用于摘要提取。
- `get_screenshot` — 将页面截图为图像（验证你的注释）

**表单操作：**
- `fill_form` — 填写命名字段：`fields: [{name, value}, ...]`

## 注释类型

所有注释都需要`id`（唯一字符串）、`type`、`page`（从1开始）。
坐标为PDF点（1/72英寸），原点为**左上角**，Y轴向下增大。美国Letter纸为612×792pt。

| 类型 | 关键属性 | 用途 |
|------|----------------|---------|
| `highlight` | `rects`, `color?`, `content?` | 标记重要文本 |
| `underline` | `rects`, `color?` | 强调词语 |
| `strikethrough` | `rects`, `color?` | 标记删除内容 |
| `note` | `x`, `y`, `content`, `color?` | 便利贴注释 |
| `freetext` | `x`, `y`, `content`, `fontSize?` | 页面上的可见文字 |
| `rectangle` | `x`, `y`, `width`, `height`, `color?`, `fillColor?` | 框选区域 |
| `circle` | `x`, `y`, `width`, `height`, `color?`, `fillColor?` | 圆形区域 |
| `line` | `x1`, `y1`, `x2`, `y2`, `color?` | 画线/箭头 |
| `stamp` | `x`, `y`, `label`, `color?`, `rotation?` | APPROVED、DRAFT、CONFIDENTIAL等 |
| `image` | `imageUrl`, `x?`, `y?`, `width?`, `height?` | **签名、首字母缩写**、logo |

**图像注释**接受本地文件路径或HTTPS URL（不支持data: URI）。省略尺寸时自动检测。用户也可以直接拖放图像到查看器上。

## 交互式工作流

### 协作注释（AI主导）
1. `display_pdf`打开文档
2. `interact` → `get_text`读取相关页面范围以理解内容
3. 向用户提议一批注释（描述你将标记的内容）
4. 获得批准后，`interact` → `add_annotations` + `get_screenshot`
5. 向用户展示结果，询问是否需要修改，迭代进行
6. 完成后，提醒用户可从查看器工具栏下载已注释的PDF

### 表单填写（可视化，非程序化）
与无界面表单工具不同，这能给用户**实时视觉反馈**，并处理字段名称模糊/未命名的表单（标签印在页面上而非字段元数据中）。

1. `display_pdf` — 检查返回的`formFields`（名称、类型、页码、边界框）
2. 若字段名称模糊（如`Text1`、`Field_7`），对相关页面`get_screenshot`并将边界框与可视标签匹配
3. 使用**可视**标签向用户询问值，或从上下文推断
4. `interact` → `fill_form`，然后`get_screenshot`展示结果
5. 用户在查看器中确认或直接编辑

对于简单、标签清晰的表单，使用`elicit_form_inputs: true`调用`display_pdf`可预先提示用户。

### 签署（可视化，非认证）
1. 询问签名/首字母缩写图像路径
2. `display_pdf`，检查`formFields`中的签名类型字段，或询问目标页面/位置
3. `interact` → `add_annotations`，使用`type: "image"`放置在目标坐标
4. `get_screenshot`确认放置效果

**免责声明：** 此操作放置的是视觉签名图像。这**不是**经认证的加密数字签名。

## 支持的来源

- 本地文件（客户端MCP根目录下的路径）
- arXiv（`/abs/` URL自动转换为PDF）
- 任何直接的HTTPS PDF链接（bioRxiv、Zenodo、OSF等——使用直接PDF链接，而非落地页）

## 适用范围之外

- **摘要/文本提取** — 请使用原生Read工具
- **经认证的数字签名** — 仅支持图像盖章
- **创建PDF** — 此工具仅适用于已有PDF
