---
name: discover-brand
description: 跨已连接平台搜索品牌素材并生成发现报告。触发词：/discover-brand、发现品牌
user-invocable: true
---

跨用户已连接的企业平台发现品牌素材。在 Notion、Confluence、Google Drive、Box、SharePoint、Figma、Gong、Granola 和 Slack 中搜索品牌指南、风格指南、消息框架、模板及对话录音。

如果 $ARGUMENTS 包含公司名称，将其用于针对性搜索。如果指定了平台，则将搜索限制在这些平台上。

在做任何其他操作之前，简要告知用户即将发生的事情：流程将搜索其已连接的平台，生成发现报告，然后（可选地）生成品牌指南并保存到工作目录中的 `.openclaw/brand-voice-guidelines.md`。在用户明确批准之前不会保存任何内容。将说明控制在 2-3 句话内 — 不要重复完整的工作流程。

遵循 discover-brand 技能说明：
1. 检查 `.openclaw/brand-voice.local.md` 中的设置（公司名称、启用平台、搜索深度）
2. 验证平台覆盖范围（如果没有文档平台则停止，如果有缺口则警告）
3. 简短确认用户范围（哪些平台，是否包含录音？）
4. 委托给 discover-brand 代理进行自主 4 阶段搜索
5. 呈现带来源、品牌元素、冲突和开放性问题的结构化发现报告
6. 提供后续步骤：生成指南、解决开放性问题、保存报告或扩展搜索

**平台验证：**
- 如果**没有连接任何平台**，告知用户此插件支持哪些 MCP 服务器（Notion、Atlassian Confluence、Box、Figma、Gong、Granola、Microsoft 365），以及 Google Drive 和 Slack 可作为 AI助手 原生集成使用。
- 如果**没有文档平台**（Notion、Confluence、Google Drive、Box、Microsoft 365）连接 — 只有 Slack、Gong、Granola 或 Figma 等辅助平台 — 停止并告知用户："您没有连接任何文档存储平台。品牌指南和风格指南几乎都存放在 Google Drive、SharePoint、Notion、Confluence 或 Box 上。请在运行发现之前至少连接一个。"
- 如果**没有主要文件存储**（Google Drive、Microsoft 365、Box）连接，警告："您的主要文件存储平台均未连接。品牌文档经常存放在这些平台上。发现将继续进行，但结果可能存在明显缺口。"
- 如果**只连接了一个平台**，警告："发现最好有 2 个以上平台进行跨来源验证。单一平台的结果置信度较低。"
