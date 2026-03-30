---
name: instrument-data-to-allotrope
description: 将实验室仪器输出文件（PDF、CSV、Excel、TXT）转换为Allotrope Simple Model（ASM）JSON格式或扁平化2D CSV。当科学家需要为LIMS系统、数据湖或下游分析标准化仪器数据时使用本技能。支持仪器类型自动检测。输出包括完整ASM JSON、便于导入的扁平化CSV，以及供数据工程师使用的可导出Python代码。常见触发场景包括：转换仪器文件、标准化实验室数据、准备上传至LIMS/ELN系统的数据，或生成用于生产流程的解析器代码。触发词：仪器数据转换、Allotrope格式、instrument-data-to-allotrope
---

# 仪器数据转Allotrope转换器

将仪器文件转换为标准化的Allotrope Simple Model（ASM）格式，用于LIMS上传、数据湖或移交给数据工程团队。

> **注意：这是一个示例技能**
>
> 本技能演示了技能如何支持您的数据工程任务——自动化模式转换、解析仪器输出并生成生产就绪代码。
>
> **针对您的组织自定义：**
> - 修改 `references/` 文件以包含您公司的特定模式或本体映射
> - 使用MCP服务器连接定义您模式的系统（如您的LIMS、数据目录或模式注册表）
> - 扩展 `scripts/` 以处理专有仪器格式或内部数据标准
>
> 此模式可适用于任何需要在格式间转换或针对组织标准进行验证的数据转换工作流。

## 工作流程概览

1. **检测仪器类型**——从文件内容自动检测（或用户指定）
2. **解析文件**——使用allotropy库（原生）或灵活回退解析器
3. **生成输出**：
   - ASM JSON（完整语义结构）
   - 扁平化CSV（2D表格格式）
   - Python解析器代码（用于数据工程师移交）
4. **交付**——文件附带摘要和使用说明

> **不确定时：** 如果您不确定如何将字段映射到ASM（例如，这是原始数据还是计算数据？设备设置还是环境条件？），请向用户寻求澄清。参阅 `references/field_classification_guide.md` 获取指导，但当歧义仍然存在时，与用户确认而非猜测。

## 快速入门

```python
# 首先安装依赖
pip install allotropy pandas openpyxl pdfplumber --break-system-packages

# 核心转换
from allotropy.parser_factory import Vendor
from allotropy.to_allotrope import allotrope_from_file

# 使用allotropy转换
asm = allotrope_from_file("instrument_data.csv", Vendor.BECKMAN_VI_CELL_BLU)
```

## 输出格式选择

**ASM JSON（默认）** - 带有本体URI的完整语义结构
- 最适合：期望ASM格式的LIMS系统、数据湖、长期存档
- 根据Allotrope模式进行验证

**扁平化CSV** - 2D表格表示
- 最适合：快速分析、Excel用户、不支持JSON的系统
- 每个测量成为一行，元数据重复

**两种格式** - 同时生成两种格式以获得最大灵活性

## 计算数据处理

**重要：** 将原始测量值与计算/派生值分离。

- **原始数据** → `measurement-document`（仪器直接读数）
- **计算数据** → `calculated-data-aggregate-document`（派生值）

计算值必须通过 `data-source-aggregate-document` 包含可追溯性：

```json
"calculated-data-aggregate-document": {
  "calculated-data-document": [{
    "calculated-data-identifier": "SAMPLE_B1_DIN_001",
    "calculated-data-name": "DNA integrity number",
    "calculated-result": {"value": 9.5, "unit": "(unitless)"},
    "data-source-aggregate-document": {
      "data-source-document": [{
        "data-source-identifier": "SAMPLE_B1_MEASUREMENT",
        "data-source-feature": "electrophoresis trace"
      }]
    }
  }]
}
```

**各仪器类型的常见计算字段：**
| 仪器 | 计算字段 |
|------|----------|
| 细胞计数器 | 活力%、稀释校正后的细胞密度 |
| 分光光度计 | 由吸光度得出的浓度、260/280比值 |
| 酶标仪 | 由标准曲线得出的浓度、%CV |
| 电泳 | DIN/RIN、区域浓度、平均大小 |
| qPCR | 相对定量、倍变化 |

详细的原始值与计算值分类指导，请参阅 `references/field_classification_guide.md`。

## 验证

在向用户交付之前，始终验证ASM输出：

```bash
python scripts/validate_asm.py output.json
python scripts/validate_asm.py output.json --reference known_good.json  # 与参考比较
python scripts/validate_asm.py output.json --strict  # 将警告视为错误
```

**验证规则：**
- 基于Allotrope ASM规范（2024年12月）
- 最后更新：2026-01-07
- 来源：https://gitlab.com/allotrope-public/asm

**软验证方式：**
未知的技术、单位或样本角色生成**警告**（而非错误），以允许向前兼容。如果Allotrope在2024年12月后添加新值，验证器不会阻止它们——而是标记供人工核查。如需更严格验证，使用 `--strict` 模式将警告视为错误。

**检查内容：**
- 正确的技术选择（如多分析物分析与酶标仪）
- 字段命名规范（空格分隔，非连字符）
- 计算数据具有可追溯性（`data-source-aggregate-document`）
- 测量值和计算值存在唯一标识符
- 必要的元数据存在
- 有效的单位和样本角色（对未知值进行软验证）

## 支持的仪器

完整列表请参阅 `references/supported_instruments.md`。主要仪器：

| 类别 | 仪器 |
|------|------|
| 细胞计数 | Vi-CELL BLU、Vi-CELL XR、NucleoCounter |
| 分光光度计 | NanoDrop One/Eight/8000、Lunatic |
| 酶标仪 | SoftMax Pro、EnVision、Gen5、CLARIOstar |
| ELISA | SoftMax Pro、BMG MARS、MSD Workbench |
| qPCR | QuantStudio、Bio-Rad CFX |
| 色谱 | Empower、Chromeleon |

## 检测与解析策略

### 第一层：原生allotropy解析（首选）
**始终先尝试allotropy。** 直接检查可用供应商：

```python
from allotropy.parser_factory import Vendor

# 列出所有支持的供应商
for v in Vendor:
    print(f"{v.name}")

# 常见供应商：
# AGILENT_TAPESTATION_ANALYSIS  （用于TapeStation XML）
# BECKMAN_VI_CELL_BLU
# THERMO_FISHER_NANODROP_EIGHT
# MOLDEV_SOFTMAX_PRO
# APPBIO_QUANTSTUDIO
# ... 还有更多
```

**当用户提供文件时，在回退到手动解析之前检查allotropy是否支持它。** `scripts/convert_to_asm.py` 的自动检测仅覆盖部分allotropy供应商。

### 第二层：灵活回退解析
**仅在allotropy不支持该仪器时使用。** 此回退：
- 不生成 `calculated-data-aggregate-document`
- 不包含完整的可追溯性
- 产生简化的ASM结构

使用灵活解析器：
- 列名模糊匹配
- 从标题提取单位
- 从文件结构提取元数据

### 第三层：PDF提取
对于仅有PDF的文件，使用pdfplumber提取表格，然后应用第二层解析。

## 解析前检查清单

在编写自定义解析器之前，始终执行：

1. **检查allotropy是否支持** - 如果可用，使用原生解析器
2. **查找参考ASM文件** - 检查 `references/examples/` 或询问用户
3. **查看仪器专用指南** - 检查 `references/instrument_guides/`
4. **对照参考验证** - 运行 `validate_asm.py --reference <file>`

## 常见错误及正确做法

| 错误 | 正确做法 |
|------|----------|
| Manifest作为对象 | 使用URL字符串 |
| 检测类型小写 | 使用"Absorbance"而非"absorbance" |
| "emission wavelength setting" | 发射使用"detector wavelength setting" |
| 所有测量在同一文档中 | 按孔/样品位置分组 |
| 缺少流程元数据 | 每个测量提取所有设备设置 |

## 为数据工程师导出代码

生成科学家可以移交的独立Python脚本：

```python
# 导出解析器代码
python scripts/export_parser.py --input "data.csv" --vendor "VI_CELL_BLU" --output "parser_script.py"
```

导出的脚本：
- 除pandas/allotropy外无外部依赖
- 包含内联文档
- 可在Jupyter notebooks中运行
- 可直接用于数据流程

## 文件结构

```
instrument-data-to-allotrope/
├── SKILL.md                          # 本文件
├── scripts/
│   ├── convert_to_asm.py            # 主转换脚本
│   ├── flatten_asm.py               # ASM → 2D CSV转换
│   ├── export_parser.py             # 生成独立解析器代码
│   └── validate_asm.py              # 验证ASM输出质量
└── references/
    ├── supported_instruments.md     # 完整仪器列表及供应商枚举
    ├── asm_schema_overview.md       # ASM结构参考
    ├── field_classification_guide.md # 不同字段类型的放置位置
    └── flattening_guide.md          # 扁平化工作原理
```

## 使用示例

### 示例1：Vi-CELL BLU文件
```
用户："将这份细胞计数数据转换为Allotrope格式"
[上传viCell_Results.xlsx]

AI助手：
1. 检测到Vi-CELL BLU（95%置信度）
2. 使用allotropy原生解析器转换
3. 输出：
   - viCell_Results_asm.json（完整ASM）
   - viCell_Results_flat.csv（2D格式）
   - viCell_parser.py（可导出代码）
```

### 示例2：代码移交请求
```
用户："我需要给我们的数据工程师解析NanoDrop文件的代码"

AI助手：
1. 生成独立Python脚本
2. 包含样本输入/输出
3. 记录所有假设
4. 提供Jupyter notebook版本
```

### 示例3：LIMS就绪的扁平化输出
```
用户："将这份ELISA数据转换为可上传到我们LIMS的CSV"

AI助手：
1. 解析酶标仪数据
2. 生成扁平化CSV，列包括：
   - sample_identifier、well_position、measurement_value、measurement_unit
   - instrument_serial_number、analysis_datetime、assay_type
3. 针对常见LIMS导入要求进行验证
```

## 实现说明

### 安装allotropy
```bash
pip install allotropy --break-system-packages
```

### 处理解析失败
如果allotropy原生解析失败：
1. 记录错误供调试
2. 回退到灵活解析器
3. 向用户报告元数据完整性降低
4. 建议从仪器导出不同格式

### ASM模式验证
在可用时对照Allotrope模式验证输出：
```python
import jsonschema
# 模式URL在references/asm_schema_overview.md中
```
