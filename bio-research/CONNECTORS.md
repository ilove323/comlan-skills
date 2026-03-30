# 连接器

## 工具引用的工作方式

插件文件使用`~~category`作为用户在该类别中连接的任何工具的占位符。例如，`~~literature`可以表示PubMed、bioRxiv或任何其他具有MCP服务器的文献来源。

插件是**工具无关的**——它们以类别（文献、临床试验、化合物数据库等）而非特定产品来描述工作流程。`.mcp.json`预配置了特定的MCP服务器，但该类别中的任何MCP服务器均可使用。

## 本插件的连接器

| 类别 | 占位符 | 已包含服务器 | 其他选项 |
|------|--------|------------|---------|
| 文献 | `~~literature` | PubMed、bioRxiv | Google Scholar、Semantic Scholar |
| 科学插图 | `~~scientific illustration` | BioRender | — |
| 临床试验 | `~~clinical trials` | ClinicalTrials.gov | 欧盟临床试验注册库 |
| 化合物数据库 | `~~chemical database` | ChEMBL | PubChem、DrugBank |
| 药物靶点 | `~~drug targets` | Open Targets | UniProt、STRING |
| 数据仓库 | `~~data repository` | Synapse | Zenodo、Dryad、Figshare |
| 期刊访问 | `~~journal access` | Wiley Scholar Gateway | Elsevier、Springer Nature |
| AI研究 | `~~AI research` | Owkin | — |
| 实验室平台 | `~~lab platform` | Benchling\* | — |

\* 占位符——MCP URL尚未配置
