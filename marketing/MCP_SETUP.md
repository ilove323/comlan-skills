# MCP服务配置指南

本插件通过MCP服务连接您的营销工具、分析平台和内容系统，实现数据自动拉取和跨工具流程集成。

## 已配置的MCP服务

| 服务名称 | URL | 用途 |
|---------|-----|------|
| slack | https://mcp.slack.com/mcp | 团队协作——分享内容草稿、报告和活动简报 |
| canva | https://mcp.canva.com/mcp | 设计——创建和编辑营销设计资产 |
| figma | https://mcp.figma.com/mcp | 设计——访问设计文件和品牌资产 |
| hubspot | https://mcp.hubspot.com/anthropic | 营销自动化——拉取活动数据、管理联系人 |
| amplitude | https://mcp.amplitude.com/mcp | 产品分析——拉取用户行为数据用于效果报告 |
| amplitude-eu | https://mcp.eu.amplitude.com/mcp | 产品分析（欧洲区）——欧区用户行为数据 |
| notion | https://mcp.notion.com/mcp | 知识库——访问简报、风格指南和活动文档 |
| ahrefs | https://api.ahrefs.com/mcp/mcp | SEO——关键词调研、反向链接分析和站点审查 |
| similarweb | https://mcp.similarweb.com | 竞争分析——竞争对手流量分析和市场对标 |
| klaviyo | https://mcp.klaviyo.com/mcp | 邮件营销——起草和管理邮件序列和活动 |
| supermetrics | https://mcp.supermetrics.com/mcp | 营销分析——整合多平台数据用于综合报告 |
| google-calendar | https://gcal.mcp.claude.com/mcp | 日历——规划内容日历和活动时间表 |
| gmail | https://gmail.mcp.claude.com/mcp | 邮件——分享报告和内部协作 |

## OpenClaw配置示例

将以下配置添加至您的 `.mcp.json` 文件（已包含在本插件目录的 `.mcp.json` 中）：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "canva": {
      "type": "http",
      "url": "https://mcp.canva.com/mcp"
    },
    "figma": {
      "type": "http",
      "url": "https://mcp.figma.com/mcp"
    },
    "hubspot": {
      "type": "http",
      "url": "https://mcp.hubspot.com/anthropic"
    },
    "amplitude": {
      "type": "http",
      "url": "https://mcp.amplitude.com/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "ahrefs": {
      "type": "http",
      "url": "https://api.ahrefs.com/mcp/mcp"
    },
    "similarweb": {
      "type": "http",
      "url": "https://mcp.similarweb.com"
    },
    "klaviyo": {
      "type": "http",
      "url": "https://mcp.klaviyo.com/mcp"
    },
    "supermetrics": {
      "type": "http",
      "url": "https://mcp.supermetrics.com/mcp"
    },
    "google-calendar": {
      "type": "http",
      "url": "https://gcal.mcp.claude.com/mcp"
    },
    "gmail": {
      "type": "http",
      "url": "https://gmail.mcp.claude.com/mcp"
    }
  }
}
```

## OAuth认证说明

部分服务需要OAuth认证。首次连接时，OpenClaw会引导您完成授权流程：

- **Slack**：在Slack中授权OpenClaw应用
- **Canva**：使用您的Canva账户授权
- **Figma**：使用您的Figma账户授权
- **HubSpot**：使用您的HubSpot账户授权
- **Notion**：使用您的Notion账户授权
- **Klaviyo**：使用您的Klaviyo账户授权
- **Google服务**（Google Calendar、Gmail）：使用您的Google账户授权

**需要API密钥的服务：**
- **Ahrefs**：在Ahrefs账户设置中获取API密钥
- **Similarweb**：在Similarweb账户中获取API访问权限
- **Amplitude**：在Amplitude项目设置中获取API凭证
- **Supermetrics**：在Supermetrics账户中配置数据源连接

## 无集成时的使用方式

即使不连接任何MCP服务，本插件的所有功能仍可正常使用——您可以通过粘贴数据、描述品牌规范或手动提供信息来完成所有任务。连接MCP服务后，AI助手可以自动拉取数据并跨工具操作，大幅提升工作效率。
