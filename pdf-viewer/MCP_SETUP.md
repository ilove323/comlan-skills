# MCP 服务器配置

本插件通过本地MCP服务器提供PDF查看和注释功能。以下是服务器的配置说明。

## 服务列表

| 服务名称 | URL / 运行方式 | 用途 |
|----------|---------------|------|
| pdf | 本地stdio（通过 `npx` 运行） | PDF查看、注释、表单填写、签名 |

## OpenClaw 配置

将以下内容添加到你的 `openclaw.json` 配置文件中：

```json
{
  "mcpServers": {
    "pdf": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-pdf", "--stdio"]
    }
  }
}
```

## 注意事项

- 此服务器在本地运行，无需API密钥或OAuth认证
- 首次运行时 `npx` 会自动安装 `@modelcontextprotocol/server-pdf` 包
- 需要 Node.js >= 18
- 访问远程PDF资源（arXiv、bioRxiv等）需要网络连接
