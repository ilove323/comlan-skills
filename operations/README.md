# 运营插件

这是一款主要为 [OpenClaw](https://openclaw.ai) 设计的业务运营插件——同时也适用于 Claude Code。帮助处理供应商管理、流程文档、变更管理、容量规划、合规跟踪和资源规划。适用于任何运营团队——可单独使用（提供您的输入），连接 ITSM、项目追踪器等工具后效果更佳。

## 安装

```bash
openclaw skills install operations
```

## 命令

通过斜杠命令显式调用的工作流：

| 命令 | 描述 |
|---|---|
| `/vendor-review` | 评估供应商——成本分析、风险评估、合同摘要和续约建议 |
| `/process-doc` | 记录业务流程——流程图、RACI 矩阵、SOP 和操作手册 |
| `/change-request` | 创建变更管理请求——影响分析、回滚计划、审批路由 |
| `/capacity-plan` | 规划资源容量——工作量分析、人员规模建模、利用率预测 |
| `/status-report` | 生成状态报告——项目更新、KPI、风险和领导层行动项 |
| `/runbook` | 创建或更新操作运行手册——定期任务的分步骤操作程序 |

所有命令均可**单独使用**（提供上下文和详情），连接 MCP 连接器后效果**大幅提升**。

## 技能

AI助手在相关时自动使用的领域知识：

| 技能 | 描述 |
|---|---|
| `vendor-management` | 评估、比较和管理供应商关系——合同、绩效、风险 |
| `process-optimization` | 分析和改善业务流程——识别瓶颈、减少浪费、简化工作流 |
| `change-management` | 规划和执行组织或技术变更——沟通、培训、推广 |
| `risk-assessment` | 识别、评估和缓解运营风险——风险登记、影响分析、控制措施 |
| `compliance-tracking` | 跟踪合规要求——审计、认证、监管截止日期、政策遵从 |
| `resource-planning` | 规划和优化资源分配——容量、利用率、预测、预算 |

## 示例工作流

### 评估供应商

```
/vendor-review
```

提供供应商名称、合同详情，或上传提案。获取包含成本分析、风险标记和建议的结构化评估报告。

### 记录流程

```
/process-doc employee offboarding
```

描述流程或向我演示一遍。获取包含流程图、RACI 矩阵和分步骤操作程序的完整 SOP。

### 提交变更请求

```
/change-request
```

描述变更内容。获取影响分析、风险评估、回滚计划和沟通模板，准备好提交审批。

### 规划容量

```
/capacity-plan
```

上传团队数据或描述您的资源情况。获取利用率分析、瓶颈识别和人员配置建议。

### 领导层状态报告

```
/status-report
```

AI助手将从您连接的工具中获取更新（或向您询问），生成包含 KPI、风险和下一步行动的精美状态报告。

### 创建操作手册

```
/runbook monthly close process
```

向我演示一遍流程。AI助手将其整理为包含检查清单、故障排除和升级路径的可重复操作手册。

## 单独使用 + 连接增强

每个命令和技能均无需集成即可使用：

| 可执行操作 | 单独使用 | 连接后增强 |
|-----------------|------------|-------------------|
| 供应商评审 | 提供详情、上传提案 | 采购系统、知识库 |
| 流程文档 | 描述流程 | 知识库（现有文档） |
| 变更请求 | 描述变更内容 | ITSM、项目追踪器 |
| 容量规划 | 上传数据、描述团队 | 项目追踪器（工作量数据） |
| 状态报告 | 手动提供更新 | 项目追踪器、聊天工具、日历 |
| 操作手册 | 演示流程 | 知识库、ITSM |

## MCP 集成

> 如果您看到不熟悉的占位符或需要检查已连接的工具，请参阅 [CONNECTORS.md](CONNECTORS.md)。

连接您的工具以获得更丰富的体验：

| 类别 | 示例 | 功能 |
|---|---|---|
| **ITSM** | ServiceNow、Zendesk | 工单管理、变更请求、事件跟踪 |
| **项目追踪器** | Asana、Jira、monday.com | 项目状态、资源分配、任务跟踪 |
| **知识库** | Notion、Confluence | 流程文档、操作手册、政策 |
| **聊天工具** | Slack、Teams | 团队协调、审批、状态更新 |
| **日历** | Google Calendar、Microsoft 365 | 会议安排、截止日期跟踪 |
| **电子邮件** | Gmail、Microsoft 365 | 供应商沟通、审批 |

完整集成列表请参阅 [CONNECTORS.md](CONNECTORS.md)。

## 配置

在 `operations/.openclaw/settings.local.json` 创建本地配置文件进行个性化设置：

```json
{
  "company": "Your Company",
  "team": "Operations",
  "reportingCadence": "weekly",
  "approvalChain": ["Manager", "Director", "VP"],
  "complianceFrameworks": ["SOC 2", "ISO 27001"],
  "fiscalYearStart": "January"
}
```

如果未配置，插件将以交互方式向您询问这些信息。
