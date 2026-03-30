# 连接器

## 本地MCP服务器

此插件使用**本地MCP服务器**而非远程连接器。PDF服务器通过`npx`在你的机器上运行。

| 类别 | 服务器 | 运行方式 |
|----------|--------|-------------|
| PDF查看与注释 | `@modelcontextprotocol/server-pdf` | 通过`npx`本地stdio运行（自动安装） |

### 系统要求
- Node.js >= 18
- 访问远程PDF（arXiv、bioRxiv等）需要网络连接
- 无需API密钥或身份验证
