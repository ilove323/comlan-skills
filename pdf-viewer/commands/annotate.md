---
name: annotate
description: 协作注释PDF——提出标记方案，共同审阅，迭代修改。触发词：/annotate、注释PDF、给PDF加注释
user-invocable: true
---

> 如需查看已连接的工具，请参阅 [CONNECTORS.md](../CONNECTORS.md)。

# 注释PDF

与用户逐节浏览文档，逐步提议并应用注释。每批操作后用户在实时查看器中审阅，再继续下一步。

## 工作流（AI主导默认模式）

1. **打开** — `display_pdf`（若文档已打开则使用现有`viewUUID`）
2. **理解** — `interact` → `get_text` 读取第一个页面范围（≤20页）
3. **提议** — 向用户描述计划注释的内容：
   > "我将在第2页高亮终止条款，在旁边添加注释'审阅30天窗口期'，并在第1页盖上DRAFT印章。可以吗？"
4. **应用** — 获得批准后，`interact`批量执行：
   `add_annotations` + `get_screenshot`（截取受影响页面）
5. **审阅** — 展示截图，询问是否需要修改
6. **迭代** — 移至下一节，重复上述步骤
7. **完成** — 提醒用户可从查看器工具栏下载已注释的PDF

## 手动模式

若用户给出明确指令（如"高亮第3段"、"在每页盖CONFIDENTIAL印章"），跳过提议步骤直接执行。仍需通过截图确认效果。

## 可用注释类型

- **文本标记：** `highlight_text`（自动定位文本——推荐）、highlight、underline、strikethrough
- **注释：** note（便利贴）、freetext（显示在页面上）
- **形状：** rectangle、circle、line
- **印章：** 任意标签——APPROVED、DRAFT、CONFIDENTIAL、REVIEWED
- **图像：** 签名、首字母缩写、logo（参见`/pdf-viewer:sign`）

## 使用技巧

- 对于文本优先使用`highlight_text`而非手动设置`rects`——它能自动查找坐标
- 在一次`interact`调用中批量执行相关注释
- 每批操作结束时加上`get_screenshot`，让用户看到结果
- 每批保持小规模（3–5个注释），便于审阅
