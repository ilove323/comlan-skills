# MCP服务器配置

本插件预配置了以下MCP服务器。将以下内容添加到您的MCP配置中（OpenClaw用户：`~/.openclaw/openclaw.json`或项目级`.mcp.json`）：

```json
{
  "mcpServers": {
    "pubmed": {
      "type": "http",
      "url": "https://pubmed.mcp.claude.com/mcp"
    },
    "biorender": {
      "type": "http",
      "url": "https://mcp.services.biorender.com/mcp"
    },
    "biorxiv": {
      "type": "http",
      "url": "https://mcp.deepsense.ai/biorxiv/mcp"
    },
    "c-trials": {
      "type": "http",
      "url": "https://mcp.deepsense.ai/clinical_trials/mcp"
    },
    "chembl": {
      "type": "http",
      "url": "https://mcp.deepsense.ai/chembl/mcp"
    },
    "synapse": {
      "type": "http",
      "url": "https://mcp.synapse.org/mcp"
    },
    "wiley": {
      "type": "http",
      "url": "https://connector.scholargateway.ai/mcp"
    },
    "owkin": {
      "type": "http",
      "url": "https://mcp.k.owkin.com/mcp"
    },
    "ot": {
      "type": "http",
      "url": "https://mcp.platform.opentargets.org/mcp"
    },
    "benchling": {
      "type": "http",
      "url": ""
    }
  }
}
```

## 服务器说明

| 服务器 | 提供商 | 类别 | 功能 |
|--------|--------|------|------|
| `pubmed` | 美国国家医学图书馆 | `~~literature` | 检索生物医学文献和研究文章 |
| `biorxiv` | deepsense.ai | `~~literature` | 访问bioRxiv和medRxiv预印本 |
| `wiley` | John Wiley & Sons | `~~journal access` | 访问学术研究和出版物 |
| `synapse` | Sage Bionetworks | `~~data repository` | 协作研究数据管理 |
| `chembl` | deepsense.ai | `~~chemical database` | 生物活性类药物化合物数据库 |
| `ot` | OpenTargets | `~~drug targets` | 药物靶点发现和优先级排序 |
| `c-trials` | deepsense.ai | `~~clinical trials` | NIH/NLM临床试验注册库 |
| `biorender` | BioRender | `~~scientific illustration` | 科学插图创作 |
| `owkin` | Owkin | `~~AI research` | 生物学AI——组织病理学和药物发现 |
| `benchling` | Benchling | `~~lab platform` | 实验室数据管理平台（需配置URL） |

## 可选二进制MCP服务器

以下服务器需要单独下载二进制文件：

- **10X Genomics txg-mcp**（`~~genomics platform`）——云分析数据和工作流程（[GitHub](https://github.com/10XGenomics/txg-mcp/releases)）
- **ToolUniverse**（`~~tool database`）——来自哈佛MIMS的科学发现AI工具（[GitHub](https://github.com/mims-harvard/ToolUniverse/releases)）

## 注意事项

- **Benchling**：需要在配置中填写您的Benchling MCP URL（目前为空）
- 所有其他服务器均为HTTP类型，可直接使用
- 各MCP服务器可能有自己的认证要求，请查阅各服务器文档
