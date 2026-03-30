---
name: create-cowork-plugin
description: >
  通过对话引导用户从零开始创建新的OpenClaw插件。
  触发词：创建插件、新建插件、create plugin
compatibility: 需要OpenClaw桌面应用环境，并能访问用于交付 .plugin 文件的输出目录。
---

# 创建 OpenClaw 插件

通过引导式对话从零开始构建新插件。带领用户完成发现、规划、设计、实现和打包全流程——最终交付可直接安装的 `.plugin` 文件。

## 概述

插件是一个自包含目录，通过技能、代理、钩子和MCP服务器集成来扩展AI助手的能力。本技能包含完整的插件架构知识和通过对话创建插件的五阶段工作流。

创建流程：

1. **发现** — 了解用户想要构建什么
2. **组件规划** — 确定需要哪些组件类型
3. **设计与澄清问题** — 详细指定每个组件
4. **实现** — 创建所有插件文件
5. **审阅与打包** — 交付 `.plugin` 文件

> **非技术性输出**：所有面向用户的对话保持简明语言。除非用户询问，否则不暴露文件路径、目录结构或schema字段等实现细节。一切都以插件的功能为框架来表述。

## 插件架构

### 目录结构

每个插件遵循以下布局：

```
plugin-name/
├── openclaw.plugin.json      # 必需：插件清单
├── skills/                   # 技能（包含SKILL.md的子目录）
│   └── skill-name/
│       ├── SKILL.md
│       └── references/
├── agents/                   # 子代理定义（.md文件）
├── .mcp.json                 # MCP服务器定义
└── README.md                 # 插件文档
```

> **旧版 `commands/` 格式**：旧版插件可能包含带有单文件 `.md` 斜杠命令的 `commands/` 目录。此格式仍然有效，但新插件应使用 `skills/*/SKILL.md` 代替——OpenClaw界面将两者都呈现为单一的"技能"概念，且技能格式通过 `references/` 支持渐进式披露。

**规则：**

- `openclaw.plugin.json` 始终是必需的
- 组件目录（`skills/`、`agents/`）放在插件根目录
- 只为插件实际使用的组件创建目录
- 所有目录和文件名使用kebab-case（小写加连字符）

### plugin.json 清单

位于 `openclaw.plugin.json`。最小必需字段为 `name`。

```json
{
  "name": "plugin-name",
  "version": "0.1.0",
  "description": "插件用途的简短说明",
  "author": {
    "name": "作者名称"
  }
}
```

**名称规则：** kebab-case，小写加连字符，不含空格或特殊字符。
**版本：** semver格式（MAJOR.MINOR.PATCH）。从 `0.1.0` 开始。

可选字段：`homepage`、`repository`、`license`、`keywords`。

可指定自定义组件路径（补充而非替代自动发现）：

```json
{
  "commands": "./custom-commands",
  "agents": ["./agents", "./specialized-agents"],
  "hooks": "./config/hooks.json",
  "mcpServers": "./.mcp.json"
}
```

### 组件Schema

各组件类型的详细schema在 `references/component-schemas.md` 中。概要：

| 组件                          | 位置            | 格式                      |
| ---------------------------------- | ------------------- | --------------------------- |
| 技能（Skills）                             | `skills/*/SKILL.md` | Markdown + YAML frontmatter |
| MCP服务器                        | `.mcp.json`         | JSON                        |
| 代理（OpenClaw中不常用） | `agents/*.md`       | Markdown + YAML frontmatter |
| 钩子（OpenClaw中很少用）      | `hooks/hooks.json`  | JSON                        |
| 命令（旧版）                  | `commands/*.md`     | Markdown + YAML frontmatter |

此schema与AI助手代码系统共享，但你创建的是用于OpenClaw的插件——这是一款用于知识工作的桌面应用。OpenClaw用户通常认为技能最有用。**用 `skills/*/SKILL.md` 搭建新插件骨架——除非用户明确需要旧版单文件格式，否则不要创建 `commands/`。**

### 使用 `~~` 占位符的可定制插件

> **默认情况下不要使用或询问此模式。** 只有在用户明确表示希望组织外部人员使用该插件时，才引入 `~~` 占位符。
> 如果看起来用户想要对外分发插件，可以提及这是一个选项，但不要用AskUserQuestion主动询问。

当插件用于分享给公司外部人员时，可能需要根据个别用户进行调整。
此时需要按类别而非特定产品引用外部工具（例如用"项目跟踪工具"代替"Jira"）。
需要分发时，使用通用语言并用两个波浪符标记需要定制的部分，例如 `在 ~~project tracker 中创建任务`。
如果使用了工具类别，在插件根目录创建 `CONNECTORS.md` 文件进行说明：

```markdown
# Connectors

## How tool references work

Plugin files use `~~category` as a placeholder for whatever tool the user
connects in that category. Plugins are tool-agnostic — they describe
workflows in terms of categories rather than specific products.

## Connectors for this plugin

| Category        | Placeholder         | Options                         |
| --------------- | ------------------- | ------------------------------- |
| Chat            | `~~chat`            | Slack, Microsoft Teams, Discord |
| Project tracker | `~~project tracker` | Linear, Asana, Jira             |
```

### ${CLAUDE_PLUGIN_ROOT} 变量

在钩子和MCP配置中，对所有插件内部路径引用使用 `${CLAUDE_PLUGIN_ROOT}`。永远不要硬编码绝对路径。

## 引导式工作流

向用户提问时使用AskUserQuestion。不要假设"行业标准"默认值是正确的。注意：AskUserQuestion始终包含"跳过"按钮和自由文本输入框，因此不要将"无"或"其他"作为选项。

### 阶段1：发现

**目标**：了解用户想构建什么以及原因。

询问（仅询问不明确的内容——若用户的初始请求已经回答，跳过相关问题）：

- 这个插件应该做什么？解决什么问题？
- 谁会在什么场景下使用它？
- 是否需要集成外部工具或服务？
- 是否有类似的插件或工作流可供参考？

总结理解内容并在继续前确认。

**输出**：对插件用途和范围的清晰陈述。

### 阶段2：组件规划

**目标**：确定插件需要哪些组件类型。

根据发现阶段的答案，确定：

- **技能** — 是否需要AI助手按需加载的专业知识，或用户发起的操作？（领域专业知识、参考schema、工作流指南、部署/配置/分析/审阅操作）
- **MCP服务器** — 是否需要外部服务集成？（数据库、API、SaaS工具）
- **代理（不常见）** — 是否有自主多步骤任务？（验证、生成、分析）
- **钩子（很少见）** — 某些事件是否应该自动触发操作？（执行策略、加载上下文、验证操作）

呈现组件规划表，包括决定不创建的组件类型：

```
| 组件 | 数量 | 用途 |
|-----------|-------|---------|
| 技能    | 3     | X的领域知识、/do-thing、/check-thing |
| 代理    | 0     | 不需要 |
| 钩子     | 1     | 验证写入操作 |
| MCP       | 1     | 连接服务Y |
```

在继续前获得用户确认或调整意见。

**输出**：已确认的待创建组件列表。

### 阶段3：设计与澄清问题

**目标**：详细指定每个组件。在实现前解决所有模糊之处。

对规划中的每种组件类型，提出针对性设计问题。按组件类型分组呈现问题，等待答案后再继续。

**技能：**

- 哪些用户查询应触发此技能？
- 它涵盖哪些知识领域？
- 是否应包含详细内容的参考文件？
- 若技能代表用户发起的操作：接受哪些参数，需要哪些工具？（Read、Write、Bash、Grep等）

**代理：**

- 每个代理应主动触发还是仅在被请求时触发？
- 需要哪些工具？
- 输出格式应是什么？

**钩子：**

- 针对哪些事件？（PreToolUse、PostToolUse、Stop、SessionStart等）
- 什么行为——验证、阻止、修改还是添加上下文？
- 基于提示（LLM驱动）还是基于命令（确定性脚本）？

**MCP服务器：**

- 什么服务器类型？（本地进程用stdio，带OAuth的托管服务用SSE，REST API用HTTP）
- 什么身份验证方式？
- 应暴露哪些工具？

若用户说"你觉得什么好就用什么"，提供具体建议并获得明确确认。

**输出**：每个组件的详细规格说明。

### 阶段4：实现

**目标**：按照最佳实践创建所有插件文件。

**操作顺序：**

1. 创建插件目录结构
2. 创建 `openclaw.plugin.json` 清单
3. 创建每个组件（精确格式参见 `references/component-schemas.md`）
4. 创建记录插件的 `README.md`

**实现指南：**

- **技能**使用渐进式披露：简洁的SKILL.md主体（3000字以内），详细内容放在 `references/` 中。Frontmatter描述必须使用第三人称并包含具体触发词组。技能主体是写给AI助手的指令，而非给用户看的消息——以指令形式撰写。
- **代理**需要包含展示触发条件的 `<example>` 块的描述，以及markdown主体中的系统提示。
- **钩子**配置放在 `hooks/hooks.json`。脚本路径使用 `${CLAUDE_PLUGIN_ROOT}`。复杂逻辑优先使用基于提示的钩子。
- **MCP配置**放在插件根目录的 `.mcp.json` 中。本地服务器路径使用 `${CLAUDE_PLUGIN_ROOT}`。在README中记录所需的环境变量。

### 阶段5：审阅与打包

**目标**：交付完成的插件。

1. 总结创建的内容——列出每个组件及其用途
2. 询问用户是否需要任何调整
3. 运行 `openclaw plugin validate <path-to-plugin-json>` 检查插件结构。若此命令不可用（例如在OpenClaw内部运行时），手动验证结构：
   - `openclaw.plugin.json` 存在且包含有效JSON，至少有 `name` 字段
   - `name` 字段为kebab-case（仅小写字母、数字和连字符）
   - 插件引用的任何组件目录（`commands/`、`skills/`、`agents/`、`hooks/`）实际存在并包含预期格式的文件——commands/skills/agents为 `.md`，hooks为 `.json`
   - 每个技能子目录包含 `SKILL.md`
   - 以与CLI验证器相同的方式报告通过和未通过的项

   在继续前修复所有错误。
4. 打包为 `.plugin` 文件：

```bash
cd /path/to/plugin-dir && zip -r /tmp/plugin-name.plugin . -x "*.DS_Store" && cp /tmp/plugin-name.plugin /path/to/outputs/plugin-name.plugin
```

> **重要**：始终先在 `/tmp/` 中创建zip，然后再复制到输出文件夹。直接写入输出文件夹可能因权限问题失败。

> **命名**：使用 `plugin.json` 中的插件名称作为 `.plugin` 文件名（例如，若名称为 `code-reviewer`，输出 `code-reviewer.plugin`）。

`.plugin` 文件将在聊天中显示为富预览，用户可以浏览文件并通过点击按钮接受插件。

## 最佳实践

- **从小开始**：从最小可行的组件集开始。一个精心设计的技能比五个半成品组件更有用。
- **技能的渐进式披露**：核心知识放在SKILL.md中，详细参考材料放在 `references/` 中，可运行示例放在 `examples/` 中。
- **清晰的触发词组**：技能描述应包含用户会说的具体词组。代理描述应包含 `<example>` 块。
- **技能是写给AI助手的**：技能主体内容写成AI助手要遵循的指令，而非给用户阅读的文档。
- **指令式写作风格**：技能中使用动词开头的指令（"解析配置文件"，而非"你应该解析配置文件"）。
- **可移植性**：对插件内部路径始终使用 `${CLAUDE_PLUGIN_ROOT}`，永远不要硬编码路径。
- **安全性**：凭据使用环境变量，远程服务器使用HTTPS，遵循最小权限原则访问工具。

## 附加资源

- **`references/component-schemas.md`** — 每种组件类型的详细格式规格（技能、代理、钩子、MCP、旧版命令、CONNECTORS.md）
- **`references/example-plugins.md`** — 三个不同复杂程度的完整插件结构示例
