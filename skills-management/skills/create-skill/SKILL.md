---
name: create-skill
description: >
  通过对话引导用户从零开始创建新的OpenClaw技能包。
  触发词：创建技能、新建插件、create skill、新建skill
compatibility: 需要OpenClaw桌面应用环境，并能访问 ~/.openclaw/skills/ 目录。
---

# 创建 OpenClaw 技能包

通过引导式对话从零开始构建新技能包。带领用户完成发现、规划、设计、实现和部署全流程。

## 概述

技能包是一个自包含目录，通过技能、代理、钩子和MCP服务器集成来扩展AI助手的能力。

创建流程：

1. **发现** — 了解用户想要构建什么
2. **组件规划** — 确定需要哪些组件类型
3. **设计与澄清问题** — 详细指定每个组件
4. **实现** — 创建所有文件
5. **审阅与部署** — 交付可安装的技能包

> **非技术性输出**：所有面向用户的对话保持简明语言。除非用户询问，否则不暴露文件路径、目录结构或schema字段等实现细节。

## 技能包架构

### 目录结构

```
skill-name/
├── openclaw.plugin.json      # 必需：技能包清单
├── skills/                   # 技能（包含SKILL.md的子目录）
│   └── skill-name/
│       ├── SKILL.md
│       └── references/
├── agents/                   # 子代理定义（.md文件）
├── .mcp.json                 # MCP服务器定义
└── README.md                 # 技能包文档
```

> **旧版 `commands/` 格式**：旧版插件可能包含 `commands/` 目录。此格式仍然有效，但新技能包应使用 `skills/*/SKILL.md` 代替——OpenClaw界面将两者都呈现为单一的"技能"概念。

**规则：**

- `openclaw.plugin.json` 始终是必需的
- 组件目录（`skills/`、`agents/`）放在技能包根目录
- 只为实际使用的组件创建目录
- 所有目录和文件名使用kebab-case

### openclaw.plugin.json 清单

```json
{
  "name": "skill-name",
  "version": "0.1.0",
  "description": "技能包用途的简短说明",
  "author": {
    "name": "作者名称"
  }
}
```

**名称规则：** kebab-case，小写加连字符。
**版本：** semver格式，从 `0.1.0` 开始。

可选字段：`homepage`、`repository`、`license`、`keywords`。

可指定自定义组件路径：

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

| 组件 | 位置 | 格式 |
|------|------|------|
| 技能（Skills） | `skills/*/SKILL.md` | Markdown + YAML frontmatter |
| MCP服务器 | `.mcp.json` | JSON |
| 代理（Agents） | `agents/*.md` | Markdown + YAML frontmatter |
| 钩子（Hooks，较少用） | `hooks/hooks.json` | JSON |
| 命令（旧版） | `commands/*.md` | Markdown + YAML frontmatter |

**用 `skills/*/SKILL.md` 搭建新技能包骨架——除非用户明确需要旧版单文件格式，否则不要创建 `commands/`。**

### 使用 `~~` 占位符的可定制技能包

> **默认情况下不要使用或询问此模式。** 只有在用户明确表示希望组织外部人员使用该技能包时，才引入 `~~` 占位符。

分发时使用通用语言，用两个波浪符标记需要定制的部分，例如 `在 ~~project tracker 中创建任务`。
如果使用了工具类别，在根目录创建 `CONNECTORS.md` 文件：

```markdown
# Connectors

## How tool references work

Skill files use `~~category` as a placeholder for whatever tool the user
connects in that category. Skills are tool-agnostic.

## Connectors for this skill

| Category        | Placeholder         | Options                         |
| --------------- | ------------------- | ------------------------------- |
| Chat            | `~~chat`            | Slack, Microsoft Teams, Discord |
| Project tracker | `~~project tracker` | Linear, Asana, Jira             |
```

### `{baseDir}` 变量

在钩子和MCP配置中，对所有技能包内部路径引用使用 `{baseDir}`。永远不要硬编码绝对路径。

## 引导式工作流

向用户提问时使用AskUserQuestion。不要假设"行业标准"默认值是正确的。

### 阶段1：发现

**目标**：了解用户想构建什么以及原因。

询问（仅询问不明确的内容）：

- 这个技能应该做什么？解决什么问题？
- 谁会在什么场景下使用它？
- 是否需要集成外部工具或服务？

总结理解内容并在继续前确认。

### 阶段2：组件规划

**目标**：确定技能包需要哪些组件类型。

- **技能** — AI助手按需加载的专业知识，或用户发起的操作
- **MCP服务器** — 外部服务集成（数据库、API、SaaS工具）
- **代理（不常见）** — 自主多步骤任务
- **钩子（很少见）** — 事件自动触发操作

呈现规划表后获得用户确认。

### 阶段3：设计与澄清问题

**目标**：详细指定每个组件，在实现前解决所有模糊之处。

**技能：** 哪些用户查询应触发？涵盖哪些知识领域？是否需要参考文件？

**代理：** 主动触发还是按需触发？需要哪些工具？

**钩子：** 针对哪些事件？验证、阻止还是添加上下文？

**MCP服务器：** 服务器类型？身份验证方式？

若用户说"你觉得什么好就用什么"，提供具体建议并获得确认。

### 阶段4：实现

**操作顺序：**

1. 创建技能包目录结构
2. 创建 `openclaw.plugin.json` 清单
3. 创建每个组件（格式参见 `references/component-schemas.md`）
4. 创建 `README.md`

**实现指南：**

- **技能**：简洁的SKILL.md主体（3000字以内），详细内容放在 `references/`。技能主体是写给AI助手的指令。
- **代理**：包含 `<example>` 块的描述，加上markdown主体中的系统提示。
- **钩子**：配置放在 `hooks/hooks.json`。脚本路径使用 `{baseDir}`。
- **MCP**：配置放在 `.mcp.json`。本地服务器路径使用 `{baseDir}`。

### 阶段5：审阅与部署

1. 总结创建的内容
2. 询问用户是否需要调整
3. 验证结构：
   - `openclaw.plugin.json` 存在且包含有效JSON，至少有 `name` 字段
   - `name` 字段为kebab-case
   - 每个技能子目录包含 `SKILL.md`
4. 部署：

```bash
# 复制到 OpenClaw 技能目录
cp -r /path/to/skill-name ~/.openclaw/skills/skill-name

# 或通过 ClawHub 发布后安装
openclaw skills install <skill-slug>
```

## 最佳实践

- **从小开始**：从最小可行的组件集开始。
- **渐进式披露**：核心知识放在SKILL.md，详细参考材料放在 `references/`。
- **清晰的触发词组**：描述中包含用户会说的具体词组，支持中英文触发。
- **指令式写作**：技能主体使用动词开头的指令。
- **可移植性**：内部路径始终使用 `{baseDir}`，永远不要硬编码路径。
- **安全性**：凭据使用环境变量，远程服务器使用HTTPS。

## 附加资源

- **`references/component-schemas.md`** — 每种组件类型的详细格式规格
- **`references/example-skills.md`** — 三个不同复杂程度的完整技能包结构示例
