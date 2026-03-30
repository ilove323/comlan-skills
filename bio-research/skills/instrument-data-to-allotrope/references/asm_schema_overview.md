# ASM模式概览

Allotrope简单模型（ASM）是一种基于JSON的标准，用于以语义一致的方式表示实验室仪器数据。

## 核心概念

### 结构
ASM使用层级化文档结构：
- **清单（Manifest）**——链接到本体和模式
- **数据（Data）**——按技术组织的实际测量数据

### 关键组件

```json
{
  "$asm.manifest": {
    "vocabulary": ["http://purl.allotrope.org/voc/afo/REC/2023/09/"],
    "contexts": ["http://purl.allotrope.org/json-ld/afo-context-REC-2023-09.jsonld"]
  },
  "<technique>-aggregate-document": {
    "device-system-document": { ... },
    "<technique>-document": [
      {
        "measurement-aggregate-document": {
          "measurement-document": [ ... ]
        }
      }
    ]
  }
}
```

## 必需的元数据文档

### data system document
每个ASM输出都必须包含此文档，含以下字段：
- `ASM file identifier`：输出文件名
- `data system instance identifier`：系统ID或"N/A"
- `file name`：源输入文件名
- `UNC path`：源文件路径
- `ASM converter name`：解析器标识符（例如"allotropy_beckman_coulter_biomek"）
- `ASM converter version`：版本字符串
- `software name`：生成源文件的仪器软件名称

### device system document
每个ASM输出都必须包含此文档，含以下字段：
- `equipment serial number`：主仪器序列号
- `product manufacturer`：厂商名称
- `device document`：子组件数组（探针、pods等）
  - `device type`：标准化类型（例如"liquid handler probe head"）
  - `device identifier`：逻辑名称（例如"Pod1"，而非序列号）
  - `equipment serial number`：组件序列号
  - `product manufacturer`：组件厂商

## 可用的ASM技术

官方ASM仓库包含**65种技术模式**：

```
absorbance, automated-reactors, balance, bga, binding-affinity, bulk-density,
cell-counting, cell-culture-analyzer, chromatography, code-reader, conductance,
conductivity, disintegration, dsc, dvs, electronic-lab-notebook,
electronic-spectrometry, electrophoresis, flow-cytometry, fluorescence,
foam-height, foam-qualification, fplc, ftir, gas-chromatography, gc-ms, gloss,
hot-tack, impedance, lc-ms, light-obscuration, liquid-chromatography,
loss-on-drying, luminescence, mass-spectrometry, metabolite-analyzer,
multi-analyte-profiling, nephelometry, nmr, optical-imaging, optical-microscopy,
osmolality, oven-kf, pcr, ph, plate-reader, pressure-monitoring, psd, pumping,
raman, rheometry, sem, solution-analyzer, specific-rotation, spectrophotometry,
stirring, surface-area-analysis, tablet-hardness, temperature-monitoring,
tensile-test, thermogravimetric-analysis, titration, ultraviolet-absorbance,
x-ray-powder-diffraction
```

参见：https://gitlab.com/allotrope-public/asm/-/tree/main/json-schemas/adm

## 常用ASM技术模式详情

以下是常用技术的详细信息：

### 细胞计数
模式：`cell-counting/REC/2024/09/cell-counting.schema.json`

关键字段：
- `viable-cell-density`（cells/mL）
- `viability`（百分比）
- `total-cell-count`
- `dead-cell-count`
- `cell-diameter-distribution-datum`

### 分光光度计（UV-Vis）
模式：`spectrophotometry/REC/2024/06/spectrophotometry.schema.json`

关键字段：
- `absorbance`（无量纲）
- `wavelength`（nm）
- `transmittance`（百分比）
- `pathlength`（cm）
- 带单位的`concentration`

### 酶标仪
模式：`plate-reader/REC/2024/06/plate-reader.schema.json`

关键字段：
- `absorbance`
- `fluorescence`
- `luminescence`
- `well-location`（A1-H12）
- `plate-identifier`

### qPCR
模式：`pcr/REC/2024/06/pcr.schema.json`

关键字段：
- `cycle-threshold-result`
- `amplification-efficiency`
- `melt-curve-datum`
- `target-DNA-description`

### 色谱
模式：`liquid-chromatography/REC/2023/09/liquid-chromatography.schema.json`

关键字段：
- `retention-time`（分钟）
- `peak-area`
- `peak-height`
- `peak-width`
- `chromatogram-data-cube`

## 数据模式

### 值基准（Value Datum）
带单位的简单值：
```json
{
  "value": 1.5,
  "unit": "mL"
}
```

### 聚合基准（Aggregate Datum）
相关值的集合：
```json
{
  "measurement-aggregate-document": {
    "measurement-document": [
      { "viable-cell-density": {"value": 2.5e6, "unit": "(cell/mL)"} },
      { "viability": {"value": 95.2, "unit": "%"} }
    ]
  }
}
```

### 数据立方体（Data Cube）
多维数组数据：
```json
{
  "cube-structure": {
    "dimensions": [{"@componentDatatype": "double", "concept": "elapsed time"}],
    "measures": [{"@componentDatatype": "double", "concept": "absorbance"}]
  },
  "data": {
    "dimensions": [[0, 1, 2, 3, 4]],
    "measures": [[0.1, 0.2, 0.3, 0.4, 0.5]]
  }
}
```

## 验证

根据官方模式验证ASM输出：

```python
import json
import jsonschema
from urllib.request import urlopen

# 加载ASM输出
with open("output.json") as f:
    asm = json.load(f)

# 从清单获取模式URL
schema_url = asm.get("$asm.manifest", {}).get("$ref")

# 验证（简化版——实际验证更复杂）
# 注意：完整验证需要解析$ref引用
```

## 模式仓库

官方模式：https://gitlab.com/allotrope-public/asm/-/tree/main/json-schemas/adm

模式结构：
```
json-schemas/adm/
├── cell-counting/
│   └── REC/2024/09/
│       └── cell-counting.schema.json
├── spectrophotometry/
│   └── REC/2024/06/
│       └── spectrophotometry.schema.json
├── plate-reader/
│   └── REC/2024/06/
│       └── plate-reader.schema.json
└── ...
```

## 常见问题

### 缺少字段
并非所有仪器导出都包含所有ASM字段。报告完整性：
```python
def report_completeness(asm, expected_fields):
    found = set(extract_all_fields(asm))
    missing = expected_fields - found
    return len(found) / len(expected_fields) * 100
```

### 单位差异
仪器可能使用不同的单位格式。allotropy库会对其进行标准化：
- "cells/mL" → "(cell/mL)"
- "%" → "%"
- "nm" → "nm"

### 日期格式
ASM使用ISO 8601格式：`2024-01-15T10:30:00Z`
