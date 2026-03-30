# MCP 服务器配置

此插件通过 Common Room MCP 服务器连接 Common Room，实现账户研究、联系人研究、通话准备和潜客挖掘功能。

## 服务列表

| 服务名称 | URL | 用途 |
|----------|-----|------|
| Common Room | `https://mcp.commonroom.io/mcp` | 所有技能和命令的主要数据来源：账户信号、联系人档案、潜客挖掘 |

## openclaw.json 配置片段

将以下配置加入您的 `.mcp.json` 文件：

```json
{
  "mcpServers": {
    "common-room": {
      "type": "http",
      "url": "https://mcp.commonroom.io/mcp"
    }
  }
}
```

## 可选：日历连接器

日历连接器（`~~calendar`）可在两个技能中启用自动会议查找：
- **通话准备（call-prep）** — 自动提取即将进行的会议的参与者姓名
- **每周准备简报（weekly-prep-brief）** — 获取本周的所有外部会议

如果没有日历连接器，这两个技能会优雅降级，改为向用户手动询问会议详情。

支持的日历服务：
- Google Calendar（通过 MCP）
- Outlook / Microsoft 365 日历

## 认证

1. 在 OpenClaw 或 Claude Code 中运行 `/mcp`
2. 选择 **Common Room** 服务并点击**认证**
3. 在浏览器中完成 Common Room 账户授权
4. 认证完成后，所有技能即可正常使用

如需帮助，请参阅 [Common Room 官方文档](https://docs.commonroom.io/)。
