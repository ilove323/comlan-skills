---
name: sign
description: 在PDF上放置签名或首字母缩写图像。触发词：/sign、签署PDF、电子签名
user-invocable: true
---

> 如需查看已连接的工具，请参阅 [CONNECTORS.md](../CONNECTORS.md)。

# 签署PDF

使用图像注释在文档上添加可视签名或首字母缩写。

> **免责声明：** 此操作将你的签名**图像**放置在页面上。这**不是**经认证的加密数字签名。如需具有法律效力的电子签名，请使用专用签署服务。

## 工作流

1. **获取签名图像** — 向用户询问其签名或首字母缩写的本地文件路径（PNG/JPG）。若没有，建议他们创建一个并保存到已知路径。

2. **打开PDF** — `display_pdf`（或复用现有`viewUUID`）。
   检查返回的`formFields`中的签名类型字段——其中包含页码和边界框坐标。

3. **定位目标位置** — 若有签名字段，使用其坐标。否则询问："在哪一页，页面什么位置？（例如，第3页右下角）"

4. **放置签名** — `interact` → `add_annotations`：
   ```json
   {"action": "add_annotations", "annotations": [
     {"id": "sig1", "type": "image", "page": 3,
      "imageUrl": "/path/to/signature.png",
      "x": 400, "y": 700, "width": 150}
   ]}
   ```
   若省略宽高，将从图像自动检测。

5. **验证** — 随后对该页面执行`get_screenshot`。向用户展示。如需调整位置，使用`update_annotations`。

6. **每页加首字母缩写** — 在单次`add_annotations`调用中每页批量添加一个`image`注释。

## 使用技巧

- `imageUrl`接受本地文件路径或HTTPS URL（不支持data: URI）
- 用户也可以直接将签名图像拖放到查看器上
- 坐标原点为左上角；美国Letter纸上的典型右下角签名位置约为`x: 400, y: 700`
- 配合`/pdf-viewer:fill-form`使用可完成完整的表单工作流
