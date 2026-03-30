# MCP 服务器配置

本插件需要以下 MCP 服务器才能运行。

## 所需服务

| 服务 | 用途 | 认证方式 |
|------|------|----------|
| Slack MCP | 搜索和读取 Slack 消息、频道及用户信息；发送和起草消息 | OAuth |

## 配置

将以下内容添加到您的 OpenClaw MCP 配置中：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp",
      "oauth": {
        "clientId": "1601185624273.8899143856786",
        "callbackPort": 3118
      }
    }
  }
}
```

## OAuth 授权

Slack MCP 使用 OAuth 进行身份验证。首次连接时，系统将引导您完成授权流程：

1. OpenClaw 将在本地端口 `3118` 启动回调监听器。
2. 浏览器将打开 Slack 授权页面，要求您登录并授权访问。
3. 授权完成后，凭据将自动保存。

**注意**：如果您的 Slack 工作区启用了管理员审批，您可能需要先由工作区管理员批准该应用，才能完成 OAuth 授权。

## 搜索范围说明

- `slack_search_public` — 搜索公开频道，无需额外授权。
- `slack_search_public_and_private` — 搜索包括私密频道、私信和群私信在内的所有内容。此工具会访问私人对话，使用前需获得用户明确同意。
