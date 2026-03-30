# Slack 插件

此插件包含将 Slack 与 Claude Code 集成所需的配置。该插件使您的 AI助手 能够直接与您的 Slack 工作区交互，让您能够通过自然语言搜索消息、发送通信、管理画布等。

## 功能

Slack MCP 服务器提供以下能力：

- **搜索**：查找消息、文件、用户和频道（公开和私密）
- **消息收发**：发送消息、检索频道历史和访问线程对话
- **画布**：创建和共享格式化文档，将内容导出为 Markdown
- **用户管理**：检索用户档案，包括自定义字段和状态信息

## 前提条件

设置 Slack MCP 服务器之前，请确保：

- 已安装 OpenClaw
- 您的 Slack 工作区管理员已批准 MCP 集成

## 安装

### OpenClaw

如果您使用 OpenClaw，可以通过本地克隆安装此插件：

```bash
git clone https://github.com/slackapi/slack-mcp-plugin.git
cd slack-mcp-plugin
openclaw --plugin-dir ./
```

插件加载时将自动配置 Slack MCP 服务器。系统会提示您通过 OAuth 认证到您的 Slack 工作区。

插件使用以下 MCP 配置（`.mcp.json`）：

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

## 使用示例

配置完成后，您可以通过自然语言与 Slack 交互：

- **搜索消息**："搜索上周关于产品发布的消息"
- **发送消息**："向 #general 频道发送一条消息，说部署已完成"
- **查找用户**："邮箱为 john@example.com 的用户是谁？"
- **访问讨论串**："显示该消息的对话线程"
- **创建画布**："创建一个包含我们会议记录的画布文档"

## 文档与资源

- [Slack MCP 服务器官方文档](https://docs.slack.dev/ai/mcp-server/)

## 注意事项与限制

- **仅限远程服务器**：此配置连接到 Slack 托管的 MCP 服务器。不需要也不支持本地安装。
- **需要管理员批准**：在您使用此功能之前，您的 Slack 工作区管理员必须批准 MCP 集成。

## 问题与反馈

有关 Slack MCP 服务器或集成问题，请参阅 [Slack 官方文档](https://docs.slack.dev/ai/mcp-server/) 或联系您的工作区管理员。
