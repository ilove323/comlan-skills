# MCP服务配置指南

本插件通过MCP服务连接您的HR系统和协作工具，实现数据自动填充和流程集成。

## 已配置的MCP服务

| 服务名称 | URL | 用途 |
|---------|-----|------|
| slack | https://mcp.slack.com/mcp | 团队协作——发布团队公告、协调候选人面试安排 |
| google-calendar | https://gcal.mcp.claude.com/mcp | 日历——安排面试、创建入职日历 |
| gmail | https://gmail.mcp.claude.com/mcp | 邮件——发送录用通知、候选人沟通 |
| notion | https://mcp.notion.com/mcp | 知识库——搜索员工手册、政策文档和团队Wiki |
| atlassian | https://mcp.atlassian.com/v1/mcp | 知识库——搜索Confluence中的政策和流程文档 |
| ms365 | https://microsoft365.mcp.claude.com/mcp | Office套件——文档处理、邮件发送 |

## OpenClaw配置示例

将以下配置添加至您的 `.mcp.json` 文件（已包含在本插件目录的 `.mcp.json` 中）：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp"
    },
    "google-calendar": {
      "type": "http",
      "url": "https://gcal.mcp.claude.com/mcp"
    },
    "gmail": {
      "type": "http",
      "url": "https://gmail.mcp.claude.com/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "ms365": {
      "type": "http",
      "url": "https://microsoft365.mcp.claude.com/mcp"
    }
  }
}
```

## OAuth认证说明

部分服务需要OAuth认证。首次连接时，OpenClaw会引导您完成授权流程：

- **Slack**：在Slack中授权OpenClaw应用
- **Google服务**（Google Calendar、Gmail）：使用您的Google账户授权
- **Notion**：使用您的Notion账户授权
- **Atlassian**：使用您的Atlassian账户授权（支持Jira和Confluence）
- **Microsoft 365**：使用您的企业Microsoft账户授权

## 未包含的服务（需手动配置）

以下服务需要您的HR团队或IT团队提供API访问配置：

- **HRIS系统**（如Workday、BambooHR、Rippling）——提供员工数据、组织架构和薪酬信息
- **ATS系统**（如Greenhouse、Lever、Ashby）——提供候选人管道、面试安排和录用跟踪
- **薪酬数据平台**（如Pave、Radford）——提供市场基准和薪酬带数据

## 无集成时的使用方式

即使不连接任何MCP服务，本插件的所有功能仍可正常使用——您可以通过手动提供信息、粘贴数据或描述具体情况来使用所有功能。连接相应MCP服务后，AI助手可以自动拉取数据，大幅提升工作效率。
