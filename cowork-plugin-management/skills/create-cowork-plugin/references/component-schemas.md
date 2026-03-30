# 组件 Schema

每种插件组件类型的详细格式规格。在阶段4实现组件时参考此文件。

## 技能（Skills）

**位置**：`skills/skill-name/SKILL.md`
**格式**：带YAML frontmatter的Markdown

### Frontmatter 字段

| 字段         | 必需 | 类型   | 说明                                             |
| ------------- | -------- | ------ | ------------------------------------------------------- |
| `name`        | 是      | String | 技能标识符（小写加连字符；与目录名匹配） |
| `description` | 是      | String | 第三人称描述，包含触发词组           |
| `metadata`    | 否       | Map    | 任意键值对（例如 `version`、`author`）   |

### 技能示例

```yaml
---
name: api-design
description: >
  This skill should be used when the user asks to "design an API",
  "create API endpoints", "review API structure", or needs guidance
  on REST API best practices, endpoint naming, or request/response design.
metadata:
  version: "0.1.0"
---
```

### 写作风格规则

- **Frontmatter描述**：第三人称（"This skill should be used when..."），触发词组加引号。
- **主体**：指令/动词原形（"解析配置文件"，而非"你应该解析配置文件"）。
- **长度**：SKILL.md主体保持3000字以内（理想1500-2000字）。详细内容移至 `references/`。

### 技能目录结构

```
skill-name/
├── SKILL.md              # 核心知识（必需）
├── references/           # 按需加载的详细文档
│   ├── patterns.md
│   └── advanced.md
├── examples/             # 可运行代码示例
│   └── sample-config.json
└── scripts/              # 工具脚本
    └── validate.sh
```

### 渐进式披露层级

1. **元数据**（始终在上下文中）：名称 + 描述（约100字）
2. **SKILL.md主体**（技能触发时）：核心知识（<5000字）
3. **捆绑资源**（按需）：参考文档、示例、脚本（不限量）

## 代理（Agents）

**位置**：`agents/agent-name.md`
**格式**：带YAML frontmatter的Markdown

### Frontmatter 字段

| 字段         | 必需 | 类型   | 说明                                         |
| ------------- | -------- | ------ | --------------------------------------------------- |
| `name`        | 是      | String | 小写加连字符，3-50个字符                      |
| `description` | 是      | String | 触发条件，包含 `<example>` 块       |
| `model`       | 是      | String | `inherit`、`sonnet`、`opus` 或 `haiku`             |
| `color`       | 是      | String | `blue`、`cyan`、`green`、`yellow`、`magenta`、`red` |
| `tools`       | 否       | Array  | 限制可用工具                          |

### 代理示例

```markdown
---
name: code-reviewer
description: Use this agent when the user asks for a thorough code review or wants detailed analysis of code quality, security, and best practices.

<example>
Context: User has just written a new module
user: "Can you do a deep review of this code?"
assistant: "I'll use the code-reviewer agent to provide a thorough analysis."
<commentary>
User explicitly requested a detailed review, which matches this agent's specialty.
</commentary>
</example>

<example>
Context: User is about to merge a PR
user: "Review this before I merge"
assistant: "Let me run a comprehensive review using the code-reviewer agent."
<commentary>
Pre-merge review benefits from the agent's structured analysis process.
</commentary>
</example>

model: inherit
color: blue
tools: ["Read", "Grep", "Glob"]
---

You are a code review specialist focused on identifying issues across security, performance, maintainability, and correctness.

**Your Core Responsibilities:**

1. Analyze code structure and organization
2. Identify security vulnerabilities
3. Flag performance concerns
4. Check adherence to best practices

**Analysis Process:**

1. Read all files in scope
2. Identify patterns and anti-patterns
3. Categorize findings by severity
4. Provide specific remediation suggestions

**Output Format:**
Present findings grouped by severity (Critical, Warning, Info) with:

- File path and line number
- Description of the issue
- Suggested fix
```

### 代理命名规则

- 3-50个字符
- 仅小写字母、数字、连字符
- 必须以字母数字开头和结尾
- 不含下划线、空格或特殊字符

### 颜色指南

- 蓝/青：分析、审阅
- 绿：面向成功的任务
- 黄：警告、验证
- 红：关键、安全
- 品红：创意、生成

## 钩子（Hooks）

**位置**：`hooks/hooks.json`
**格式**：JSON

### 可用事件

| 事件              | 触发时机                   |
| ------------------ | ------------------------------- |
| `PreToolUse`       | 工具调用执行前     |
| `PostToolUse`      | 工具调用完成后     |
| `Stop`             | AI助手完成响应时 |
| `SubagentStop`     | 子代理完成时        |
| `SessionStart`     | 会话开始时           |
| `SessionEnd`       | 会话结束时             |
| `UserPromptSubmit` | 用户发送消息时   |
| `PreCompact`       | 上下文压缩前       |
| `Notification`     | 通知触发时       |

### 钩子类型

**基于提示**（推荐用于复杂逻辑）：

```json
{
  "type": "prompt",
  "prompt": "Evaluate whether this file write follows project conventions: $TOOL_INPUT",
  "timeout": 30
}
```

支持的事件：Stop、SubagentStop、UserPromptSubmit、PreToolUse。

**基于命令**（确定性检查）：

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/hooks/scripts/validate.sh",
  "timeout": 60
}
```

### hooks.json 示例

```json
{
  "PreToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Check that this file write follows project coding standards. If it violates standards, explain why and block.",
          "timeout": 30
        }
      ]
    }
  ],
  "SessionStart": [
    {
      "matcher": "",
      "hooks": [
        {
          "type": "command",
          "command": "cat ${CLAUDE_PLUGIN_ROOT}/context/project-context.md",
          "timeout": 10
        }
      ]
    }
  ]
}
```

### 钩子输出格式（命令钩子）

命令钩子向stdout返回JSON：

```json
{
  "decision": "block",
  "reason": "File write violates naming convention"
}
```

决策值：`approve`（批准）、`block`（阻止）、`ask_user`（请求用户确认）。

## MCP 服务器

**位置**：插件根目录的 `.mcp.json`
**格式**：JSON

### 服务器类型

**stdio**（本地进程）：

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/servers/server.js"],
      "env": {
        "API_KEY": "${API_KEY}"
      }
    }
  }
}
```

**SSE**（远程服务器，服务器推送事件传输）：

```json
{
  "mcpServers": {
    "asana": {
      "type": "sse",
      "url": "https://mcp.asana.com/sse"
    }
  }
}
```

**HTTP**（远程服务器，可流式HTTP传输）：

```json
{
  "mcpServers": {
    "api-service": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${API_TOKEN}"
      }
    }
  }
}
```

### 环境变量展开

所有MCP配置支持 `${VAR_NAME}` 替换：

- `${CLAUDE_PLUGIN_ROOT}` — 插件目录（为保证可移植性始终使用此变量）
- `${ANY_ENV_VAR}` — 用户环境变量

在插件README中记录所有必需的环境变量。

### 没有URL的目录服务器

某些MCP目录条目没有 `url`，因为端点是动态的。插件可以通过**名称**引用这些服务器——如果插件MCP配置中的服务器名称与目录条目名称匹配，则视为与URL匹配等效。

## 命令（Commands，旧版）

> **新插件优先使用 `skills/*/SKILL.md`。** OpenClaw界面现在将命令和技能呈现为单一的"技能"概念。`commands/` 格式仍然有效，但只有在明确需要带 `$ARGUMENTS`/`$1` 替换和内联bash执行的单文件格式时才使用。

**位置**：`commands/command-name.md`
**格式**：带可选YAML frontmatter的Markdown

### Frontmatter 字段

| 字段           | 必需 | 类型            | 说明                                         |
| --------------- | -------- | --------------- | --------------------------------------------------- |
| `description`   | 否       | String          | `/help` 中显示的简短描述（60字符以内） |
| `allowed-tools` | 否       | String or Array | 命令可使用的工具                           |
| `model`         | 否       | String          | 模型覆盖：`sonnet`、`opus`、`haiku`           |
| `argument-hint` | 否       | String          | 记录预期参数以供自动补全       |

### 命令示例

```markdown
---
description: Review code for security issues
allowed-tools: Read, Grep, Bash(git:*)
argument-hint: [file-path]
---

Review @$1 for security vulnerabilities including:

- SQL injection
- XSS attacks
- Authentication bypass
- Insecure data handling

Provide specific line numbers, severity ratings, and remediation suggestions.
```

### 关键规则

- 命令是写给AI助手的指令，而非给用户的消息。以指令形式撰写。
- `$ARGUMENTS` 将所有参数捕获为单个字符串；`$1`、`$2`、`$3` 捕获位置参数。
- `@path` 语法将文件内容包含在命令上下文中。
- `` ! `` 反引号语法内联执行bash以获取动态上下文（例如 `` !`git diff --name-only` ``）。
- 使用 `${CLAUDE_PLUGIN_ROOT}` 可移植地引用插件文件。

### allowed-tools 模式

```yaml
# 指定工具
allowed-tools: Read, Write, Edit, Bash(git:*)

# 仅允许特定bash命令
allowed-tools: Bash(npm:*), Read

# MCP工具（指定）
allowed-tools: ["mcp__plugin_name_server__tool_name"]
```

## CONNECTORS.md

**位置**：插件根目录
**创建时机**：当插件按类别而非特定产品引用外部工具时

### 格式

```markdown
# Connectors

## How tool references work

Plugin files use `~~category` as a placeholder for whatever tool the user
connects in that category. For example, `~~project tracker` might mean
Asana, Linear, Jira, or any other project tracker with an MCP server.

Plugins are tool-agnostic — they describe workflows in terms of categories
rather than specific products.

## Connectors for this plugin

| Category        | Placeholder         | Included servers | Other options            |
| --------------- | ------------------- | ---------------- | ------------------------ |
| Chat            | `~~chat`            | Slack            | Microsoft Teams, Discord |
| Project tracker | `~~project tracker` | Linear           | Asana, Jira, Monday      |
```

### 使用 ~~ 占位符

在插件文件（技能、代理）中通用地引用工具：

```markdown
Check ~~project tracker for open tickets assigned to the user.
Post a summary to ~~chat in the team channel.
```

定制过程中（通过 cowork-plugin-customizer 技能），这些占位符会被替换为具体工具名称。

## README.md

每个插件都应包含一个README，内容包括：

1. **概述** — 插件的功能
2. **组件** — 技能、代理、钩子、MCP服务器的列表
3. **配置** — 所需的环境变量或配置项
4. **使用方法** — 如何触发每个技能
5. **定制** — 若存在CONNECTORS.md，加以说明
