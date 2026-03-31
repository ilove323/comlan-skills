# MCP 发现与连接

插件定制过程中如何查找并连接MCP。

## 可用工具

### `search_mcp_registry`
在MCP目录中搜索可用连接器。

**输入：** `{ "keywords": ["搜索", "词组", "数组"] }`

**输出：** 最多10条结果，每条包含：
- `name`：MCP显示名称
- `description`：一行描述
- `tools`：MCP提供的工具名称列表
- `url`：MCP端点URL（在 `.mcp.json` 中使用）
- `directoryUuid`：与 suggest_connectors 配合使用的UUID
- `connected`：布尔值——用户是否已连接此MCP

### `suggest_connectors`
显示"连接"按钮，让用户安装/连接MCP。

**输入：** `{ "directoryUuids": ["uuid1", "uuid2"] }`

**输出：** 为每个MCP渲染带"连接"按钮的UI

## 类别与关键词映射

| 类别 | 搜索关键词 |
|----------|-----------------|
| `project-management` | `["asana", "jira", "linear", "monday", "tasks"]` |
| `software-coding` | `["github", "gitlab", "bitbucket", "code"]` |
| `chat` | `["slack", "teams", "discord"]` |
| `documents` | `["google docs", "notion", "confluence"]` |
| `calendar` | `["google calendar", "calendar"]` |
| `email` | `["gmail", "outlook", "email"]` |
| `design-graphics` | `["figma", "sketch", "design"]` |
| `analytics-bi` | `["datadog", "grafana", "analytics"]` |
| `crm` | `["salesforce", "hubspot", "crm"]` |
| `wiki-knowledge-base` | `["notion", "confluence", "outline", "wiki"]` |
| `data-warehouse` | `["bigquery", "snowflake", "redshift"]` |
| `conversation-intelligence` | `["gong", "chorus", "call recording"]` |

## 工作流

1. **查找定制点**：查找带 `~~` 前缀的值（例如 `~~Jira`）
2. **检查前期发现**：你是否已经了解他们使用哪个工具？
   - **是**：搜索该特定工具获取其 `url`，跳至步骤5
   - **否**：继续步骤3
3. **搜索**：使用映射关键词调用 `search_mcp_registry`
4. **呈现选择并询问用户**：显示所有结果，询问他们使用哪个
5. **按需连接**：若未连接，调用 `suggest_connectors`
6. **更新MCP配置**：使用搜索结果中的 `url` 添加配置

## 更新插件MCP配置

### 查找配置文件

1. **检查 `plugin.json`** 中是否有 `mcpServers` 字段：
   ```json
   {
     "name": "my-plugin",
     "mcpServers": "./config/servers.json"
   }
   ```
   若存在，编辑该路径的文件。

2. **若无 `mcpServers` 字段**，使用插件根目录的 `.mcp.json`（默认）。

3. **若 `mcpServers` 仅指向 `.mcpb` 文件**（捆绑服务器），在插件根目录新建 `.mcp.json`。

### 配置文件格式

支持包装和非包装两种格式：

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

使用 `search_mcp_registry` 结果中的 `url` 字段。

### 没有URL的目录条目

某些目录条目没有 `url`，因为端点是动态的——管理员在连接服务器时提供。这些服务器仍可通过**名称**在插件的MCP配置中引用：如果配置中的MCP服务器名称与目录条目名称匹配，则视为与URL匹配等效。