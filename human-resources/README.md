# 人力资源插件

专为OpenClaw设计的人事运营插件——支持招聘、入职、绩效管理、政策查询和薪酬分析。适用于任何HR团队——单独使用时只需提供相关信息，连接HRIS、ATS和其他工具后功能将大幅增强。

## 安装

```bash
openclaw plugins add knowledge-work-plugins/human-resources
```

## 命令

通过斜杠命令触发的明确工作流：

| 命令 | 描述 |
|------|------|
| `/draft-offer` | 起草含薪酬明细、入职日期和条款的录用通知书 |
| `/onboarding` | 生成新员工入职清单和第一周计划 |
| `/performance-review` | 构建绩效评估方案——自评提示、经理评估模板、校准准备 |
| `/policy-lookup` | 查询和解释公司政策——年假、福利、报销、差旅、远程工作 |
| `/comp-analysis` | 分析薪酬数据——市场对标、薪资带定位、股权刷新建模 |
| `/people-report` | 生成人员编制、离职率、多元化或组织健康度报告 |

所有命令均支持**独立使用**（提供背景信息即可）和**连接工具后的增强模式**。

## 技能

AI助手在相关时自动调用的领域知识：

| 技能 | 描述 |
|------|------|
| `recruiting-pipeline` | 追踪和管理招聘管道——来源、筛选、面试、录用各阶段 |
| `employee-handbook` | 回答公司政策、福利和流程相关问题 |
| `compensation-benchmarking` | 对标市场数据进行薪酬分析——基本工资、股权、总薪酬 |
| `org-planning` | 人员编制规划、组织设计和团队结构优化 |
| `people-analytics` | 分析人才数据——离职趋势、敬业度信号、多元化指标 |
| `interview-prep` | 创建结构化面试方案——能力导向问题、评分卡、反馈会模板 |

## 典型工作流程示例

### 起草录用通知书

```
/draft-offer
```

告知AI助手岗位、层级、地点和薪酬详情，获得含条款、股权明细和福利摘要的完整录用通知书草稿。

### 规划新员工入职

```
/onboarding
```

提供新员工姓名、岗位、团队和入职日期，获得全面的入职清单、第一周日程安排、工具权限清单和引导人分配模板。

### 准备绩效评估

```
/performance-review
```

获取自评、经理评估和校准的模板，AI助手帮助构建具体、可操作且公正的反馈。

### 查询福利政策

直接提问：
```
我们的育儿假政策是什么？
```

`employee-handbook` 技能自动触发，在已连接的 ~~knowledge base 中搜索答案。

### 薪酬对标

```
/comp-analysis
```

上传薪酬数据或描述您的薪资带，获得市场对比、薪资带定位分析和调整建议。

## 独立使用 + 连接工具增强

每个命令和技能都可以在不集成任何系统的情况下使用：

| 使用场景 | 独立模式 | 连接工具后的增强 |
|---------|---------|--------------|
| 起草录用通知书 | 手动提供详情 | 连接HRIS、ATS自动填充 |
| 入职清单 | 描述您的流程 | 连接HRIS、知识库获取模板 |
| 绩效评估 | 手动输入 | 连接HRIS获取历史评估记录 |
| 政策查询 | 粘贴手册内容 | 连接知识库自动搜索 |
| 薪酬分析 | 上传CSV、描述薪资带 | 连接薪酬数据MCP |
| 人员报告 | 上传数据 | 连接HRIS获取实时数据 |

## MCP集成

> 如遇不熟悉的占位符或需要查看已连接工具，请参见 [CONNECTORS.md](CONNECTORS.md)。如需配置MCP服务，请参见 [MCP_SETUP.md](MCP_SETUP.md)。

连接您的工具，获得更丰富的使用体验：

| 类别 | 示例 | 功能说明 |
|------|------|---------|
| **HRIS** | Workday、BambooHR、Rippling | 员工数据、组织架构、年假余额 |
| **ATS** | Greenhouse、Lever、Ashby | 候选人管道、面试安排、录用跟踪 |
| **薪酬数据** | Pave、Radford | 市场基准、薪酬带数据 |
| **团队协作** | Slack、Teams | 团队公告、候选人协调 |
| **日历** | Google Calendar、Microsoft 365 | 面试安排、入职日历 |
| **邮件** | Gmail、Microsoft 365 | 录用通知书、候选人沟通 |

完整集成列表请参见 [CONNECTORS.md](CONNECTORS.md)。

## 设置

创建 `settings.local.json` 文件进行个性化配置：

- **OpenClaw**：保存至您已与OpenClaw共享的任意文件夹（通过文件夹选择器）。插件会自动找到该文件。
- **OpenClaw**：保存至 `human-resources/.openclaw/settings.local.json`。

```json
{
  "company": "您的公司名称",
  "headquarters": "城市，省/州",
  "employeeCount": 500,
  "benefits": {
    "healthInsurance": "保险提供商名称",
    "pto": "不限额 / X天",
    "parentalLeave": "X周"
  },
  "compensation": {
    "currency": "CNY",
    "equityType": "RSU / 期权",
    "vestingSchedule": "4年归属，1年悬崖"
  }
}
```

如未配置，插件会通过对话交互方式向您询问这些信息。
