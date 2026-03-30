---
name: generate-guidelines
description: 从文档、录音、发现报告或任意组合生成品牌声音指南。触发词：/generate-guidelines、生成指南
user-invocable: true
---

**强制第一步 — 在做任何其他事情之前执行，包括读取来源或处理参数。**检查用户在本次会话中是否选择了工作目录。在开始任何指南生成工作之前，必须先验证这一点。如果没有工作目录，停止并警告用户："您没有选择工作目录。没有工作目录，我无法将指南保存到文件 — 它们只会存在于本次对话中，不会保留到未来会话。请选择一个工作目录并重新运行此命令。如果您希望无论如何继续，请告诉我。"等待用户确认后再继续。

从用户提供的任意来源生成全面、LLM 可用的品牌声音指南 — 品牌文档、对话录音、来自 `/brand-voice:discover-brand` 的发现报告或直接输入。

处理 $ARGUMENTS 中指定的来源。如果未指定，检查：
1. 本次会话中是否生成了发现报告
2. `.openclaw/brand-voice.local.md` 中已知的品牌素材位置
3. 已连接平台（Notion、Confluence、Google Drive、Box、SharePoint、Gong）中的现有素材
4. 如果没有可用内容，建议先运行 `/brand-voice:discover-brand`

遵循 guideline-generation 技能说明：
1. 识别并分类所有可用来源（发现报告、文档、录音）
2. 根据需要委托给 document-analysis 和 conversation-analysis 代理
3. 将发现合成为统一指南，包含"我们是 / 我们不是"表格和情境语气矩阵
4. 为每个部分分配置信度评分
5. 对任何模糊性提出带代理建议的开放性问题
6. 呈现关键发现并提供后续步骤
7. 将指南保存到用户工作目录中的 `.openclaw/brand-voice-guidelines.md`（先归档任何现有文件）。请勿使用相对于代理当前工作目录的路径 — 在 OpenClaw 中，代理从插件缓存目录运行，而非用户项目目录。

生成后，指南将在本地保存，以便 `/brand-voice:enforce-voice` 在未来会话中自动找到它们。

支持的文档格式：PDF、PowerPoint、Word、Markdown、纯文本。
支持的录音来源：Gong（MCP）、Granola（MCP）、Notion 会议记录、手动上传。
