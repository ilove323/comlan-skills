# MCP 服务器配置

本插件通过 Apollo MCP 服务器连接 Apollo.io，实现线索丰富、潜客挖掘和序列管理功能。安装插件后，MCP 服务器配置将自动生效，无需手动操作。

## 服务列表

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Apollo | `https://mcp.apollo.io/mcp` | 线索丰富、公司搜索、序列管理的主数据源 |

## openclaw.json 配置片段

将以下配置加入您的 `.mcp.json` 文件：

```json
{
  "mcpServers": {
    "apollo": {
      "type": "http",
      "url": "https://mcp.apollo.io/mcp"
    }
  }
}
```

## OAuth 认证

Apollo MCP 服务器支持 OAuth 认证：

1. 在 OpenClaw 或 Claude Code 中运行 `/mcp`
2. 选择 **Apollo** 服务器并点击**认证**
3. 在浏览器中完成 Apollo.io 账户登录
4. 认证完成后，所有技能即可正常使用

如需帮助，请参阅 [Apollo.io 官方文档](https://docs.apollo.io/)。
