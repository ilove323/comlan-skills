# 支持的仪器

## 本技能可以转换什么数据？

**任何能映射到Allotrope模式的仪器数据都可以转换。** 本技能采用分层解析方法：

1. **原生allotropy解析器**（如下所列）——最高精度，针对特定厂商格式验证
2. **灵活备用解析器**——通过将列映射到ASM字段，处理任何表格数据（CSV、Excel、TXT）
3. **PDF提取**——从PDF中提取表格，然后应用灵活解析

如果您的仪器未在下方列出，只要您的数据包含可识别的测量字段（样本ID、数值、单位、时间戳等）并能映射到ASM技术模式，本技能仍可进行转换。

---

## 具有原生Allotropy解析器的仪器

以下仪器在allotropy库中具有优化的解析器及其Vendor枚举值。

## 细胞计数

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Beckman Coulter Vi-CELL BLU | `BECKMAN_VI_CELL_BLU` | .csv |
| Beckman Coulter Vi-CELL XR | `BECKMAN_VI_CELL_XR` | .txt, .xls, .xlsx |
| ChemoMetec NucleoView NC-200 | `CHEMOMETEC_NUCLEOVIEW` | .xlsx |
| ChemoMetec NC-View | `CHEMOMETEC_NC_VIEW` | .xlsx |
| Revvity Matrix | `REVVITY_MATRIX` | .csv |

## 分光光度计（UV-Vis）

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Thermo Fisher NanoDrop One | `THERMO_FISHER_NANODROP_ONE` | .csv, .xlsx |
| Thermo Fisher NanoDrop Eight | `THERMO_FISHER_NANODROP_EIGHT` | .tsv, .txt |
| Thermo Fisher NanoDrop 8000 | `THERMO_FISHER_NANODROP_8000` | .csv |
| Unchained Labs Lunatic | `UNCHAINED_LABS_LUNATIC` | .csv, .xlsx |
| Thermo Fisher Genesys 30 | `THERMO_FISHER_GENESYS30` | .csv |

## 酶标仪（多模式、吸光度、荧光）

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Molecular Devices SoftMax Pro | `MOLDEV_SOFTMAX_PRO` | .txt |
| PerkinElmer EnVision | `PERKIN_ELMER_ENVISION` | .csv |
| Agilent Gen5 (BioTek) | `AGILENT_GEN5` | .xlsx |
| Agilent Gen5 Image | `AGILENT_GEN5_IMAGE` | .xlsx |
| BMG MARS (CLARIOstar) | `BMG_MARS` | .csv, .txt |
| BMG LabTech Smart Control | `BMG_LABTECH_SMART_CONTROL` | .csv |
| Thermo SkanIt | `THERMO_SKANIT` | .xlsx |
| Revvity Kaleido | `REVVITY_KALEIDO` | .csv |
| Tecan Magellan | `TECAN_MAGELLAN` | .xlsx |

## ELISA / 免疫分析

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Molecular Devices SoftMax Pro | `MOLDEV_SOFTMAX_PRO` | .txt |
| MSD Discovery Workbench | `MSD_WORKBENCH` | .txt |
| MSD Methodical Mind | `METHODICAL_MIND` | .xlsx |
| BMG MARS | `BMG_MARS` | .csv, .txt |

## qPCR / PCR

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Applied Biosystems QuantStudio | `APPBIO_QUANTSTUDIO` | .xlsx |
| Applied Biosystems QuantStudio Design & Analysis | `APPBIO_QUANTSTUDIO_DESIGNANALYSIS` | .xlsx, .csv |
| Bio-Rad CFX Maestro | `BIORAD_CFX_MAESTRO` | .csv, .xlsx |
| Roche LightCycler | `ROCHE_LIGHTCYCLER` | .txt |

## 色谱（HPLC、LC）

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Waters Empower | `WATERS_EMPOWER` | .xml |
| Thermo Fisher Chromeleon | `THERMO_FISHER_CHROMELEON` | .xml |
| Agilent ChemStation | `AGILENT_CHEMSTATION` | .csv |

## 电泳

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Agilent TapeStation | `AGILENT_TAPESTATION` | .csv |
| PerkinElmer LabChip | `PERKIN_ELMER_LABCHIP` | .csv |

## 流式细胞术

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| BD Biosciences FACSDiva | `BD_BIOSCIENCES_FACSDIVA` | .xml |
| FlowJo | `FLOWJO` | .wsp |

## 溶液分析

| 仪器 | Vendor枚举值 | 文件类型 |
|------|------------|---------|
| Roche Cedex BioHT | `ROCHE_CEDEX_BIOHT` | .xlsx |
| Beckman Coulter Biomek | `BECKMAN_COULTER_BIOMEK` | .csv |

## 自动检测模式

本技能通过以下模式尝试从文件内容识别仪器类型：

### Vi-CELL BLU
- 列标题："Sample ID"、"Viable cells (x10^6 cells/mL)"、"Viability (%)"
- 文件结构：具有特定列顺序的CSV

### Vi-CELL XR
- 列标题："Sample"、"Total cells/ml"、"Viable cells/ml"
- 支持多种导出格式

### NanoDrop
- 列标题："Sample Name"、"Nucleic Acid Conc."、"A260"、"A280"
- 包含260/280和260/230比值列

### 酶标仪（通用）
- 孔位标识符（A1-H12模式）
- "Plate"、"Well"、"Sample"列
- 带元数据标题的块状结构

### ELISA
- 含浓度的标准曲线数据
- OD/吸光度读数
- 样本/空白/标准品分类

## 使用Vendor枚举

```python
from allotropy.parser_factory import Vendor
from allotropy.to_allotrope import allotrope_from_file

# 列出所有支持的厂商
for v in Vendor:
    print(f"{v.name}: {v.value}")

# 转换文件
asm = allotrope_from_file("data.csv", Vendor.BECKMAN_VI_CELL_BLU)
```

## 检查支持状态

```python
from allotropy.parser_factory import get_parser

# 检查某个厂商/文件组合是否受支持
try:
    parser = get_parser(Vendor.BECKMAN_VI_CELL_BLU)
    print("Supported!")
except Exception as e:
    print(f"Not supported: {e}")
```
