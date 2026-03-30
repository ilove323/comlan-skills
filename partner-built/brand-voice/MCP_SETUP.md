# MCP 服务器配置

品牌声音插件通过 7 个 MCP 服务器连接企业知识平台，实现品牌素材发现、指南生成和内容执行功能。

## 服务列表

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Notion | `https://mcp.notion.com/mcp` | 发现主干 — 通过已连接来源跨 Google Drive、SharePoint、Slack 等联合搜索 |
| Atlassian | `https://mcp.atlassian.com/v1/mcp` | Confluence 深度搜索 + Jira 背景，适用于 Atlassian 重型企业 |
| Box | `https://mcp.box.com` | 云文件存储中的官方品牌文档和风格指南 |
| Microsoft 365 | `https://microsoft365.mcp.claude.com/mcp` | SharePoint、OneDrive、Teams — 企业文档和邮件模板 |
| Figma | `https://mcp.figma.com/mcp` | 品牌设计系统，为声音和语气提供视觉品牌背景 |
| Gong | `https://mcp.gong.io/mcp` | 销售通话录音和分析，用于品牌声音模式提取 |
| Granola | `https://mcp.granola.ai/mcp` | 会议录音和记录，揭示实践中的品牌声音 |

**原生集成（无需 MCP）：** Google Drive 和 Slack 作为原生 AI助手 集成可用，无需安装 MCP 连接器。

## openclaw.json 配置片段

将以下配置加入您的 `.mcp.json` 文件：

```json
{
  "mcpServers": {
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "box": {
      "type": "http",
      "url": "https://mcp.box.com"
    },
    "figma": {
      "type": "http",
      "url": "https://mcp.figma.com/mcp"
    },
    "gong": {
      "type": "http",
      "url": "https://mcp.gong.io/mcp"
    },
    "microsoft-365": {
      "type": "http",
      "url": "https://microsoft365.mcp.claude.com/mcp"
    },
    "granola": {
      "type": "http",
      "url": "https://mcp.granola.ai/mcp"
    }
  }
}
```

## 认证说明

所有 7 个 MCP 服务器均需独立认证：

1. 在 OpenClaw 或 Claude Code 中运行 `/mcp`
2. 对每个列出的服务，点击**认证**按钮
3. 在浏览器中完成对应平台的账户授权
4. 认证完成后，发现和分析功能即可使用

**最低配置建议：**至少连接一个文档平台（Notion、Confluence、Google Drive、Box 或 Microsoft 365）才能运行品牌发现。Gong、Granola、Figma 和 Slack 是可选的补充来源。
