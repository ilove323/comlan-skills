# 将ASM扁平化为2D CSV

将层级化的ASM JSON转换为平面2D表格，用于LIMS导入、电子表格分析或数据工程流水线。

## 为何需要扁平化？

ASM语义丰富但具有层级结构。许多系统需要平面表格：
- LIMS导入（Benchling、STARLIMS、LabWare）
- Excel/CSV分析
- 数据库加载
- 快速可视化检查

## 扁平化策略

### 核心原则
每个**测量**变成一**行**。元数据在每行重复。

### 排除的内容
扁平化会有意**省略顶层ASM元数据**，例如：
- `$asm.manifest`（模型版本、模式URI）
- 技术聚合文档之外的根级字段

这使输出专注于实验数据。如果出于合规或审计目的需要追踪模式版本，考虑将原始ASM JSON与扁平化CSV一起存储，或修改扁平化脚本以包含这些字段。

### 层级到列的映射
```
ASM层级                          → 平面列
─────────────────────────────────────────────────
device-system-document.
  device-identifier              → instrument_serial_number
  model-number                   → instrument_model

measurement-aggregate-document.
  analyst                        → analyst
  measurement-time               → measurement_datetime

measurement-document[].
  sample-identifier              → sample_id
  viable-cell-density.value      → viable_cell_density
  viable-cell-density.unit       → viable_cell_density_unit
  viability.value                → viability_percent
```

## 列命名规范

使用带描述性后缀的snake_case：

| ASM字段 | 平面列 |
|---------|--------|
| `viable-cell-density` | `viable_cell_density` |
| `.value` | `_value`（如果明确则省略） |
| `.unit` | `_unit` |
| `measurement-time` | `measurement_datetime` |

## 示例：细胞计数

### ASM输入（简化）
```json
{
  "cell-counting-aggregate-document": {
    "device-system-document": {
      "device-identifier": "VCB001",
      "model-number": "Vi-CELL BLU"
    },
    "cell-counting-document": [{
      "measurement-aggregate-document": {
        "analyst": "jsmith",
        "measurement-time": "2024-01-15T10:30:00Z",
        "measurement-document": [
          {
            "sample-identifier": "Sample_A",
            "viable-cell-density": {"value": 2500000, "unit": "(cell/mL)"},
            "viability": {"value": 95.2, "unit": "%"}
          },
          {
            "sample-identifier": "Sample_B",
            "viable-cell-density": {"value": 1800000, "unit": "(cell/mL)"},
            "viability": {"value": 88.7, "unit": "%"}
          }
        ]
      }
    }]
  }
}
```

### 扁平化输出
```csv
sample_id,viable_cell_density,viable_cell_density_unit,viability_percent,analyst,measurement_datetime,instrument_serial_number,instrument_model
Sample_A,2500000,(cell/mL),95.2,jsmith,2024-01-15T10:30:00Z,VCB001,Vi-CELL BLU
Sample_B,1800000,(cell/mL),88.7,jsmith,2024-01-15T10:30:00Z,VCB001,Vi-CELL BLU
```

## 示例：酶标仪

### ASM输入（简化）
```json
{
  "plate-reader-aggregate-document": {
    "plate-reader-document": [{
      "measurement-aggregate-document": {
        "plate-identifier": "ELISA_001",
        "measurement-document": [
          {"well-location": "A1", "absorbance": {"value": 0.125, "unit": "mAU"}},
          {"well-location": "A2", "absorbance": {"value": 0.892, "unit": "mAU"}},
          {"well-location": "A3", "absorbance": {"value": 1.456, "unit": "mAU"}}
        ]
      }
    }]
  }
}
```

### 扁平化输出
```csv
plate_id,well_position,absorbance,absorbance_unit
ELISA_001,A1,0.125,mAU
ELISA_001,A2,0.892,mAU
ELISA_001,A3,1.456,mAU
```

## 处理数据立方体

数据立方体（时间序列、光谱）需要特殊处理：

### 选项1：展开为行
每个数据点变成一行：
```csv
sample_id,time_seconds,absorbance
Sample_A,0,0.100
Sample_A,60,0.125
Sample_A,120,0.150
```

### 选项2：宽格式
测量值作为列：
```csv
sample_id,abs_0s,abs_60s,abs_120s
Sample_A,0.100,0.125,0.150
```

### 选项3：单元格内的JSON数组
保持为数组格式（某些系统支持此格式）：
```csv
sample_id,absorbance_timeseries
Sample_A,"[0.100,0.125,0.150]"
```

## 各技术标准列集

### 细胞计数
```
sample_id, viable_cell_density, viable_cell_density_unit, total_cell_count,
viability_percent, average_cell_diameter, average_cell_diameter_unit,
analyst, measurement_datetime, instrument_serial_number
```

### 分光光度计
```
sample_id, wavelength_nm, absorbance, pathlength_cm, concentration,
concentration_unit, a260_a280_ratio, a260_a230_ratio,
analyst, measurement_datetime, instrument_serial_number
```

### 酶标仪 / ELISA
```
plate_id, well_position, sample_type, sample_id, absorbance, absorbance_unit,
concentration, concentration_unit, dilution_factor, cv_percent,
analyst, measurement_datetime, instrument_serial_number
```

### qPCR
```
sample_id, target_name, well_position, ct_value, ct_mean, ct_sd,
quantity, quantity_unit, amplification_efficiency,
analyst, measurement_datetime, instrument_serial_number
```

## Python实现

```python
import json
import pandas as pd

def flatten_asm(asm_dict, technique="cell-counting"):
    """
    将ASM JSON扁平化为pandas DataFrame。

    Args:
        asm_dict: 解析后的ASM JSON
        technique: ASM技术类型

    Returns:
        每行一条测量记录的pandas DataFrame
    """
    rows = []

    # 获取聚合文档
    agg_key = f"{technique}-aggregate-document"
    agg_doc = asm_dict.get(agg_key, {})

    # 提取设备信息
    device = agg_doc.get("device-system-document", {})
    device_info = {
        "instrument_serial_number": device.get("device-identifier"),
        "instrument_model": device.get("model-number")
    }

    # 获取技术文档
    doc_key = f"{technique}-document"
    for doc in agg_doc.get(doc_key, []):
        meas_agg = doc.get("measurement-aggregate-document", {})

        # 提取通用元数据
        common = {
            "analyst": meas_agg.get("analyst"),
            "measurement_datetime": meas_agg.get("measurement-time"),
            **device_info
        }

        # 提取每条测量记录
        for meas in meas_agg.get("measurement-document", []):
            row = {**common}

            # 扁平化测量字段
            for key, value in meas.items():
                if isinstance(value, dict) and "value" in value:
                    # 值基准模式
                    col = key.replace("-", "_")
                    row[col] = value["value"]
                    if "unit" in value:
                        row[f"{col}_unit"] = value["unit"]
                else:
                    row[key.replace("-", "_")] = value

            rows.append(row)

    return pd.DataFrame(rows)

# 用法
with open("asm_output.json") as f:
    asm = json.load(f)

df = flatten_asm(asm, "cell-counting")
df.to_csv("flattened_output.csv", index=False)
```

## LIMS导入注意事项

将扁平化数据导入LIMS时：
- 将列名与LIMS模式字段名匹配
- 时间戳使用ISO 8601日期格式
- 确保样本ID与LIMS中已有的样本标识符匹配
- 检查LIMS是否期望单位在独立列中或嵌入在数值中
