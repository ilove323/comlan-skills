# Apollo.io OpenClaw 插件

通过 [Apollo.io](https://www.apollo.io/) 挖掘潜客、丰富线索并加载外联序列 — 由 Apollo MCP 服务器驱动，支持**一键集成**。

---

## 一键 MCP 服务器集成

安装后，此插件会**自动配置 Apollo MCP 服务器**。无需手动搭建服务器，无需编辑配置文件 — 安装插件并使用您的 Apollo 账户完成认证即可。

---

## 强大的工作流技能

此插件提供多项高价值技能，将多个 Apollo API 串联为完整工作流：

| 技能 | 功能说明 |
|---|---|
| `/apollo:enrich-lead` | 输入姓名、LinkedIn 链接或邮箱 — 获取包含邮件、电话、公司信息及后续行动建议的完整联系人档案 |
| `/apollo:prospect` | 用自然语言描述您的 ICP — 获取一份经过排名的丰富决策者线索列表 |
| `/apollo:sequence-load` | 查找线索、丰富信息并批量加入外联序列 — 自动处理去重和入组 |

### `/apollo:enrich-lead`

输入姓名、公司、LinkedIn 链接或邮箱，获取包含邮件、电话、职位、地点、公司详情及后续行动建议的完整联系人档案。支持模糊查找（例如 "CEO of Figma"），精确匹配失败时自动回退到搜索。

### `/apollo:prospect`

用自然语言描述您的 ICP。流程将搜索匹配公司、批量丰富企业画像数据、查找决策者、通过批量丰富获取联系方式，并返回附带 ICP 匹配评分的排名线索列表。

### `/apollo:sequence-load`

查找符合目标定位条件的联系人、丰富信息、创建联系人（含去重）并批量加入现有 Apollo 序列。入组前预览候选人，完成后展示完整摘要。

---

## 安装

### OpenClaw

点击以下链接一步完成安装：

[在 OpenClaw 中安装](https://claude.ai/desktop/customize/plugins/new?marketplace=apolloio/apollo-mcp-plugin&plugin=apollo)

然后重启 OpenClaw 以确保 MCP 服务器正确启动。

### OpenClaw

#### 1. 添加此插件的应用市场

在 OpenClaw 中运行：

```
/plugin marketplace add apolloio/apollo-mcp-plugin
```

#### 2. 安装插件

```
/plugin install apollo@apollo-plugin-marketplace
```

#### 3. 重启 OpenClaw

确保 MCP 服务器正确启动。

---

## 认证

Apollo MCP 服务器支持 **OAuth** 认证：

1. 安装后，在 OpenClaw 中运行 `/mcp`
2. 选择 **Apollo** 服务器并点击**认证**
3. 在浏览器中完成 Apollo.io 登录
4. 完成 — 所有命令现已可用

---

## Apollo 积分说明

某些操作会消耗 [Apollo 积分](https://docs.apollo.io/)：

- **人员丰富**（三个技能均使用）每人消耗 1 个积分
- **批量丰富**（`/apollo:prospect`、`/apollo:sequence-load`）批次中每人消耗 1 个积分
- 消耗积分前，插件会提前提醒您

---

## 致谢

- **MCP 服务器** 由 [Apollo.io](https://docs.apollo.io/) 提供
- **插件规范** 由 [Anthropic](https://docs.anthropic.com/) 提供

---

## 许可证

MIT — 详见 [LICENSE](LICENSE)。
