# 字段分类指南

本指南帮助将仪器数据字段分类到正确的ASM文档位置。在将原始仪器输出映射到Allotrope简单模型结构时使用本指南。

## ASM文档层级

```
<technique>-aggregate-document
├── device-system-document          # 仪器硬件信息
├── data-system-document            # 软件/转换信息
├── <technique>-document[]          # 每次运行/序列的数据
│   ├── analyst                     # 执行分析的人员
│   ├── measurement-aggregate-document
│   │   ├── measurement-time
│   │   ├── measurement-document[]  # 单个测量结果
│   │   │   ├── sample-document
│   │   │   ├── device-control-aggregate-document
│   │   │   └── [measurement fields]
│   │   └── [aggregate-level metadata]
│   ├── processed-data-aggregate-document
│   │   └── processed-data-document[]
│   │       ├── data-processing-document
│   │       └── [processed results]
│   └── calculated-data-aggregate-document
│       └── calculated-data-document[]
```

## 字段分类类别

### 1. 设备/仪器信息 → `device-system-document`

物理仪器的硬件和固件详情。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 仪器名称 | `model-number` | "Vi-CELL BLU"、"NanoDrop One" |
| 序列号 | `equipment-serial-number` | "VCB-12345"、"SN001234" |
| 制造商 | `product-manufacturer` | "Beckman Coulter"、"Thermo Fisher" |
| 固件版本 | `firmware-version` | "v2.1.3" |
| 设备ID | `device-identifier` | "Instrument_01" |
| 品牌 | `brand-name` | "Beckman Coulter" |

**规则：** 如果该值描述物理仪器且在运行之间不发生变化，则放入`device-system-document`。

---

### 2. 软件/数据系统信息 → `data-system-document`

用于采集、分析或转换的软件信息。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 软件名称 | `software-name` | "Chromeleon"、"Gen5" |
| 软件版本 | `software-version` | "7.3.2" |
| 文件名 | `file-name` | "experiment_001.xlsx" |
| 文件路径 | `file-identifier` | "/data/runs/2024-01-15/" |
| 数据库ID | `ASM-converter-name` | "allotropy v0.1.55" |

**规则：** 如果该值描述软件、文件元数据或数据溯源，则放入`data-system-document`。

---

### 3. 样本信息 → `sample-document`

被分析的生物/化学样本的元数据。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 样本ID | `sample-identifier` | "Sample_A"、"LIMS-001234" |
| 样本名称 | `written-name` | "CHO Cell Culture Day 5" |
| 样本类型/角色 | `sample-role-type` | "unknown sample role"、"control sample role" |
| 批次ID | `batch-identifier` | "Batch-2024-001" |
| 描述 | `description` | "蛋白质表达样本" |
| 孔位 | `location-identifier` | "A1"、"B3" |

**规则：** 如果该值标识或描述被测量的对象（而非测量方式），则放入`sample-document`。

---

### 4. 设备控制设置 → `device-control-aggregate-document`

测量期间使用的仪器设置和参数。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 注射体积 | `sample-volume-setting` | 10 µL |
| 波长 | `detector-wavelength-setting` | 254 nm |
| 温度 | `compartment-temperature` | 37°C |
| 流速 | `flow-rate` | 1.0 mL/min |
| 曝光时间 | `exposure-duration-setting` | 500 ms |
| 检测器增益 | `detector-gain-setting` | 1.5 |
| 照明 | `illumination-setting` | 80% |

**规则：** 如果该值是影响测量的可配置仪器参数，则放入`device-control-aggregate-document`。

---

### 5. 环境条件 → `device-control-document`或技术特定位置

测量期间的环境或受控参数。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 环境温度 | `ambient-temperature` | 22.5°C |
| 湿度 | `ambient-relative-humidity` | 45% |
| 色谱柱温度 | `compartment-temperature` | 30°C |
| 样本温度 | `sample-temperature` | 4°C |
| 电泳温度 | （技术特定） | 26.4°C |

**规则：** 影响测量质量的环境条件放入设备控制或技术特定位置。

---

### 6. 原始测量数据 → `measurement-document`

仪器直接读数——"原始真实"数据。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 吸光度 | `absorbance` | 0.523 AU |
| 荧光 | `fluorescence` | 12500 RFU |
| 细胞计数 | `total-cell-count` | 2.5e6个细胞 |
| 峰面积 | `peak-area` | 1234.5 mAU·min |
| 保留时间 | `retention-time` | 5.67 min |
| Ct值 | `cycle-threshold-result` | 24.5 |
| 浓度（测量值） | `mass-concentration` | 1.5 mg/mL |

**规则：** 如果该值是未经本次分析中其他值计算的仪器直接读数，则放入`measurement-document`。

---

### 7. 计算/派生数据 → `calculated-data-aggregate-document`

由原始测量值计算得出的数值。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 活力% | `calculated-result` | 95.2% |
| 浓度（来自标准曲线） | `calculated-result` | 125 ng/µL |
| 比值（260/280） | `calculated-result` | 1.89 |
| 相对量 | `calculated-result` | 2.5x |
| 回收率% | `calculated-result` | 98.7% |
| CV% | `calculated-result` | 2.3% |

**计算数据文档结构：**
```json
{
  "calculated-data-name": "viability",
  "calculated-result": {"value": 95.2, "unit": "%"},
  "calculation-description": "viable cells / total cells * 100"
}
```

**规则：** 如果该值由本次分析中的其他测量值计算得出，则放入`calculated-data-aggregate-document`。尽可能包含`calculation-description`。

---

### 8. 处理/分析数据 → `processed-data-aggregate-document`

数据处理算法（峰积分、细胞分类等）的结果。

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 峰列表 | `peak-list` | 积分峰结果 |
| 细胞大小分布 | `cell-diameter-distribution` | 直方图数据 |
| 基线校正数据 | （在processed-data-document中） | 校正后光谱 |
| 拟合曲线 | （在processed-data-document中） | 标准曲线拟合 |

**关联的`data-processing-document`：**
```json
{
  "cell-type-processing-method": "trypan blue exclusion",
  "cell-density-dilution-factor": {"value": 2, "unit": "(unitless)"},
  "minimum-cell-diameter-setting": {"value": 5, "unit": "µm"},
  "maximum-cell-diameter-setting": {"value": 50, "unit": "µm"}
}
```

**规则：** 如果该值来自算法或对原始数据的处理方法，则放入`processed-data-aggregate-document`，处理参数放入`data-processing-document`。

---

### 9. 时间/时间戳 → 各种位置

| 时间戳类型 | 位置 | ASM字段 |
|----------|------|---------|
| 测量时间 | `measurement-document` | `measurement-time` |
| 运行开始时间 | `analysis-sequence-document` | `analysis-sequence-start-time` |
| 运行结束时间 | `analysis-sequence-document` | `analysis-sequence-end-time` |
| 数据导出时间 | `data-system-document` | （自定义） |

**规则：** 使用ISO 8601格式：`2024-01-15T10:30:00Z`

---

### 10. 分析员/操作员信息 → `<technique>-document`

| 字段类型 | ASM字段 | 示例 |
|---------|---------|------|
| 操作员姓名 | `analyst` | "jsmith" |
| 审阅者 | （自定义或扩展） | "Pending" |

**规则：** 分析员信息放在技术文档级别，而非单个测量结果中。

---

## 决策树

```
该字段关于...

仪器本身？
├── 硬件规格 → device-system-document
└── 软件/文件 → data-system-document

样本？
└── 样本ID、名称、类型、批次 → sample-document

仪器设置？
└── 可配置参数 → device-control-aggregate-document

环境条件？
└── 温度、湿度等 → device-control-document

直接读数？
└── 原始仪器输出 → measurement-document

计算值？
├── 来自其他测量值 → calculated-data-document
└── 来自处理算法 → processed-data-document

时间？
├── 测量时间 → measurement-document.measurement-time
└── 运行开始/结束时间 → analysis-sequence-document

谁执行的？
└── 操作员/分析员 → <technique>-document.analyst
```

## 常见仪器到ASM的映射

> **注意：** 这些映射来源于[Benchling allotropy库](https://github.com/Benchling-Open-Source/allotropy/tree/main/src/allotropy/parsers)。如需权威映射，请查阅您特定仪器的解析器源代码。

### 细胞计数器（Vi-CELL BLU）
*来源：`allotropy/parsers/beckman_vi_cell_blu/vi_cell_blu_structure.py`*

| 仪器字段 | ASM字段 |
|---------|---------|
| Sample ID | `sample_identifier` |
| Analysis date/time | `measurement_time` |
| Analysis by | `analyst` |
| Viability (%) | `viability` |
| Viable (x10^6) cells/mL | `viable_cell_density` |
| Total (x10^6) cells/mL | `total_cell_density` |
| Cell count | `total_cell_count` |
| Viable cells | `viable_cell_count` |
| Average diameter (μm) | `average_total_cell_diameter` |
| Average viable diameter (μm) | `average_live_cell_diameter` |
| Average circularity | `average_total_cell_circularity` |
| Cell type | `cell_type_processing_method`（data-processing） |
| Dilution | `cell_density_dilution_factor`（data-processing） |
| Min/Max Diameter | `minimum/maximum_cell_diameter_setting`（data-processing） |

### 分光光度计（NanoDrop）
| 仪器字段 | ASM字段 |
|---------|---------|
| Sample Name | `sample_identifier` |
| A260, A280 | `absorbance`（带波长） |
| Concentration | `mass_concentration` |
| 260/280 ratio | `a260_a280_ratio` |
| Pathlength | `pathlength` |

### 酶标仪
| 仪器字段 | ASM字段 |
|---------|---------|
| Well | `location_identifier` |
| Sample Type | `sample_role_type` |
| Absorbance/OD | `absorbance` |
| Fluorescence | `fluorescence` |
| Plate ID | `container_identifier` |

### 色谱（HPLC）
| 仪器字段 | ASM字段 |
|---------|---------|
| Sample ID | `sample_identifier` |
| Injection Volume | `injection_volume` |
| Retention Time | `retention_time` |
| Peak Area | `peak_area` |
| Peak Height | `peak_height` |
| Column Temp | `column_oven_temperature` |
| Flow Rate | `flow_rate` |

## 单位处理

仅使用源数据中明确存在的单位。如果某值没有指定单位：
- 使用`(unitless)`作为单位值
- 不要基于领域知识推断单位

## 计算数据可追溯性

创建计算值时，始终使用`data-source-aggregate-document`将其与源数据关联：

```json
{
    "calculated-data-name": "DIN",
    "calculated-result": {"value": 5.8, "unit": "(unitless)"},
    "calculated-data-identifier": "TEST_ID_147",
    "data-source-aggregate-document": {
        "data-source-document": [{
            "data-source-identifier": "TEST_ID_145",
            "data-source-feature": "sample"
        }]
    }
}
```

这声明："DIN 5.8是由`TEST_ID_145`处的样本计算得出的。"

**重要性：**
- **审计**：证明某值来自特定原始数据
- **调试**：将意外结果追溯到其源头
- **重新处理**：当算法变更时知道需要重新分析哪些输入

**为以下内容分配唯一ID：**
- 测量值、峰、区域和计算值
- 使用一致的命名模式（例如`INSTRUMENT_TYPE_TEST_ID_N`）

这支持双向遍历：从计算值追溯到原始值，或从原始值追溯到所有派生值。

---

## 嵌套文档结构（关键）

常见错误是将字段直接"扁平化"到测量文档上，而应该嵌套在子结构中。这会破坏模式合规性并丢失语义上下文。

### 为什么嵌套很重要

ASM使用嵌套文档进行语义分组：

| 文档 | 目的 | 包含内容 |
|------|------|---------|
| `sample document` | 被测量的对象 | 样本ID、位置、板标识符 |
| `device control aggregate document` | 仪器如何操作 | 设置、参数、技术 |
| `custom information document` | 厂商特定字段 | 不映射到ASM的非标准字段 |

### sample document字段

这些字段必须在`sample document`内，而不能扁平化到测量上：

```json
// ❌ 错误——字段扁平化到测量上
{
  "measurement identifier": "TEST_001",
  "sample identifier": "Sample_A",
  "location identifier": "A1",
  "absorbance": {"value": 0.5, "unit": "(unitless)"}
}

// ✅ 正确——字段嵌套在sample document中
{
  "measurement identifier": "TEST_001",
  "sample document": {
    "sample identifier": "Sample_A",
    "location identifier": "A1",
    "well plate identifier": "96WP001"
  },
  "absorbance": {"value": 0.5, "unit": "(unitless)"}
}
```

**属于sample document的字段：**
- `sample identifier`——样本ID/名称
- `written name`——描述性样本名称
- `batch identifier`——批次/批号
- `sample role type`——标准、空白、对照、未知
- `location identifier`——孔位（A1、B3等）
- `well plate identifier`——板条码
- `description`——样本描述

### device control document字段

仪器设置必须在`device control aggregate document`内：

```json
// ❌ 错误——设备设置扁平化
{
  "measurement identifier": "TEST_001",
  "device identifier": "Pod1",
  "technique": "Custom",
  "volume": {"value": 26, "unit": "μL"}
}

// ✅ 正确——设置嵌套在device control中
{
  "measurement identifier": "TEST_001",
  "device control aggregate document": {
    "device control document": [{
      "device type": "liquid handler",
      "device identifier": "Pod1"
    }]
  },
  "aspiration volume": {"value": 26, "unit": "μL"}
}
```

**属于device control的字段：**
- `device type`——设备类型
- `device identifier`——设备ID
- `detector wavelength setting`——检测波长
- `compartment temperature`——温度设置
- `sample volume setting`——体积设置
- `flow rate`——流速设置

### custom information document

不能映射到标准ASM术语的厂商特定字段放入`custom information document`：

```json
"device control document": [{
  "device type": "liquid handler",
  "custom information document": {
    "probe": "2",
    "pod": "Pod1",
    "source labware name": "Inducer",
    "destination labware name": "GRP1"
  }
}]
```

### 液体处理器：转移配对

对于液体处理器，一次测量代表一次完整的转移（吸取+分配），而不是独立的操作：

```json
// ❌ 错误——吸取和分配分开记录
[
  {"measurement identifier": "OP_001", "transfer type": "Aspirate", "volume": {"value": 26, "unit": "μL"}},
  {"measurement identifier": "OP_002", "transfer type": "Dispense", "volume": {"value": 26, "unit": "μL"}}
]

// ✅ 正确——单条记录含源和目标
{
  "measurement identifier": "TRANSFER_001",
  "sample document": {
    "source well location identifier": "1",
    "destination well location identifier": "2",
    "source well plate identifier": "96WP001",
    "destination well plate identifier": "96WP002"
  },
  "aspiration volume": {"value": 26, "unit": "μL"},
  "transfer volume": {"value": 26, "unit": "μL"}
}
```

**配对逻辑：**
1. 按探针编号匹配吸取和分配操作
2. 每个匹配对创建一条测量记录
3. 使用`source_*`字段表示吸取位置
4. 使用`destination_*`字段表示分配位置
5. 同时包含`aspiration volume`和`transfer volume`

### 快速参考：嵌套决策

```
该字段关于...

被测量的样本？
├── 样本ID、名称、批次 → sample document
├── 孔位 → sample document.location identifier
├── 板条码 → sample document.well plate identifier
└── 源/目标位置 → sample document（带前缀）

仪器设置？
├── 标准设置 → device control aggregate document
└── 厂商特定 → custom information document

测量值？
└── 直接放在measurement document上（例如吸光度、体积）

转移操作类型？
└── 不要使用"transfer type"——配对成含源/目标字段的
    单条测量记录
```

### 验证

使用`validate_asm.py`检查嵌套问题：
```bash
python scripts/validate_asm.py output.json --reference known_good.json
```

验证器检查：
- 字段错误地扁平化到测量上
- 缺少`sample document`包装器
- 缺少`device control aggregate document`包装器
- 厂商字段缺少`custom information document`
- 液体处理器：独立的转移类型而非配对记录

## 来源

- [Allotrope简单模型介绍](https://www.allotrope.org/introduction-to-allotrope-simple-model)
- [Benchling allotropy库](https://github.com/Benchling-Open-Source/allotropy)
- [Allotrope Foundation ASM概述](https://www.allotrope.org/asm)
