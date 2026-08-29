---
name: xlsx-processing
description: >
  当用户需要处理 Excel 文件时使用 — 包括读取、写入、公式、图表、
  条件格式、数据验证、数据透视表或大文件处理。
  触发条件：程序化生成 Excel 报表、读取电子表格数据、
  添加公式或图表、应用条件格式、执行数据验证、
  生成数据透视表、处理 CSV 导入/导出、处理 Excel 格式的大数据集。
---

# XLSX 处理

## 概述

使用 openpyxl 进行富格式处理，使用 pandas 进行数据分析，以编程方式操作 Excel 文件。本技能涵盖读取/写入电子表格、公式、图表、条件格式、数据验证、数据透视表生成、CSV 导入/导出以及处理大文件的策略。

当需要通过代码而非手动编辑来创建、读取、转换或丰富 Excel 文件时，应用本技能。

## 多阶段流程

### 阶段 1：需求分析

1. 确定操作类型（读取、写入、转换、生成报表）
2. 识别数据源和数据量
3. 定义格式和公式要求
4. 规划工作表结构和命名
5. 评估性能需求（行数、文件大小）

> **停止 — 在知道行数以及是否需要格式之前，不要开始实现（这决定了库的选择）。**

### 阶段 2：实现

1. 选择库（参见决策表）
2. 实现数据加载和转换
3. 应用格式、公式和验证
4. 添加图表和条件格式
5. 优化文件大小和内存使用

> **停止 — 对于超过 10,000 行的文件，不要跳过内存优化。**

### 阶段 3：验证

1. 在 Excel、LibreOffice 和 Google Sheets 中打开
2. 验证公式计算正确
3. 检查格式渲染一致
4. 用边界情况测试（空数据、最大行数）
5. 验证数据准确性

## 库选择决策表

| 场景                       | 推荐库              | 原因               |
| -------------------------- | ------------------- | ------------------ |
| 富格式（颜色、边框、字体） | openpyxl            | 完整的格式 API     |
| 数据分析、聚合、数据透视   | pandas              | DataFrame 操作     |
| 基于数据分析生成带格式报表 | pandas + openpyxl   | 结合两者优势       |
| 仅读取数据，无需格式       | pandas              | 最简单的 API       |
| 大文件（> 1 万行），写密集 | openpyxl write_only | 流式写入，低内存   |
| 大文件（> 1 万行），读密集 | openpyxl read_only  | 流式读取，低内存   |
| CSV 与 Excel 互转          | pandas              | 单行操作           |
| 电子表格中的图表           | openpyxl            | 完全控制的图表 API |

## openpyxl 模式

### 创建工作簿

```python
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side
from openpyxl.utils import get_column_letter

wb = Workbook()
ws = wb.active
ws.title = "Report"

# 表头行
headers = ['Name', 'Department', 'Revenue', 'Target', 'Achievement']
header_font = Font(name='Calibri', size=11, bold=True, color='FFFFFF')
header_fill = PatternFill(start_color='2F5496', end_color='2F5496', fill_type='solid')
header_alignment = Alignment(horizontal='center', vertical='center')
thin_border = Border(
    left=Side(style='thin'),
    right=Side(style='thin'),
    top=Side(style='thin'),
    bottom=Side(style='thin'),
)

for col, header in enumerate(headers, 1):
    cell = ws.cell(row=1, column=col, value=header)
    cell.font = header_font
    cell.fill = header_fill
    cell.alignment = header_alignment
    cell.border = thin_border

# 数据行
for row_idx, row_data in enumerate(data, 2):
    for col_idx, value in enumerate(row_data, 1):
        cell = ws.cell(row=row_idx, column=col_idx, value=value)
        cell.border = thin_border

# 自动调整列宽
for col in range(1, len(headers) + 1):
    max_length = max(
        len(str(ws.cell(row=row, column=col).value or ''))
        for row in range(1, ws.max_row + 1)
    )
    ws.column_dimensions[get_column_letter(col)].width = min(max_length + 2, 50)

# 冻结表头行
ws.freeze_panes = 'A2'

wb.save('report.xlsx')
```

### 公式

```python
# 基本公式
ws['E2'] = '=C2/D2'                    # 除法
ws['F2'] = '=SUM(C2:C100)'             # 求和
ws['G2'] = '=AVERAGE(C2:C100)'         # 平均值
ws['H2'] = '=COUNTIF(E2:E100,">1")'   # 条件计数
ws['I2'] = '=IF(E2>=1,"Met","Below")'  # 条件判断
ws['J2'] = '=VLOOKUP(A2,Sheet2!A:B,2,FALSE)'  # 查找

# 数组公式（Excel 365 动态数组）
ws['K2'] = '=UNIQUE(A2:A100)'

# 命名区域
from openpyxl.workbook.defined_name import DefinedName
ref = f"Report!$C$2:$C${len(data)+1}"
defn = DefinedName('RevenueRange', attr_text=ref)
wb.defined_names.add(defn)
```

### 图表

```python
from openpyxl.chart import BarChart, LineChart, PieChart, Reference

# 柱状图
chart = BarChart()
chart.type = 'col'
chart.title = 'Revenue by Department'
chart.y_axis.title = 'Revenue ($)'
chart.x_axis.title = 'Department'
chart.style = 10  # 内置样式

data_ref = Reference(ws, min_col=3, min_row=1, max_row=ws.max_row)
cats_ref = Reference(ws, min_col=2, min_row=2, max_row=ws.max_row)
chart.add_data(data_ref, titles_from_data=True)
chart.set_categories(cats_ref)
chart.width = 20
chart.height = 12

ws.add_chart(chart, 'G2')

# 折线图（多系列）
line = LineChart()
line.title = 'Monthly Trends'
for col in range(3, 6):
    values = Reference(ws, min_col=col, min_row=1, max_row=13)
    line.add_data(values, titles_from_data=True)
cats = Reference(ws, min_col=1, min_row=2, max_row=13)
line.set_categories(cats)
ws.add_chart(line, 'G20')
```

### 条件格式

```python
from openpyxl.formatting.rule import CellIsRule, ColorScaleRule, DataBarRule

# 高亮超过阈值的单元格
ws.conditional_formatting.add(
    'C2:C100',
    CellIsRule(operator='greaterThan', formula=['100000'],
              fill=PatternFill(bgColor='C6EFCE'))
)

# 低于目标值标红
ws.conditional_formatting.add(
    'E2:E100',
    CellIsRule(operator='lessThan', formula=['1'],
              fill=PatternFill(bgColor='FFC7CE'),
              font=Font(color='9C0006'))
)

# 色阶（绿到红）
ws.conditional_formatting.add(
    'E2:E100',
    ColorScaleRule(
        start_type='min', start_color='F8696B',
        mid_type='percentile', mid_value=50, mid_color='FFEB84',
        end_type='max', end_color='63BE7B'
    )
)

# 数据条
ws.conditional_formatting.add(
    'C2:C100',
    DataBarRule(start_type='min', end_type='max', color='638EC6')
)
```

### 数据验证

```python
from openpyxl.worksheet.datavalidation import DataValidation

# 下拉列表
dv = DataValidation(type='list', formula1='"Active,Inactive,Pending"', allow_blank=True)
dv.error = 'Please select a valid status'
dv.errorTitle = 'Invalid Entry'
ws.add_data_validation(dv)
dv.add('D2:D100')

# 数值范围
nv = DataValidation(type='whole', operator='between', formula1=0, formula2=100)
nv.error = 'Value must be between 0 and 100'
ws.add_data_validation(nv)
nv.add('F2:F100')

# 日期验证
dv_date = DataValidation(type='date', operator='greaterThan', formula1='2025-01-01')
ws.add_data_validation(dv_date)
dv_date.add('G2:G100')
```

## pandas 模式

### 读取 Excel

```python
import pandas as pd

# 读取单个工作表
df = pd.read_excel('data.xlsx', sheet_name='Sheet1')

# 带选项读取
df = pd.read_excel('data.xlsx',
    sheet_name='Sales',
    header=0,
    usecols='A:E',
    dtype={'ID': str, 'Revenue': float},
    parse_dates=['Date'],
    na_values=['N/A', 'null', ''],
)

# 读取所有工作表
sheets = pd.read_excel('data.xlsx', sheet_name=None)  # DataFrame 字典
```

### 使用 pandas + openpyxl 写入 Excel

```python
with pd.ExcelWriter('output.xlsx', engine='openpyxl') as writer:
    df_summary.to_excel(writer, sheet_name='Summary', index=False)
    df_detail.to_excel(writer, sheet_name='Detail', index=False)

    # 访问 openpyxl 工作簿进行格式设置
    wb = writer.book
    ws = wb['Summary']
    # 应用格式...
```

### 数据透视表

```python
# 创建数据透视表
pivot = pd.pivot_table(
    df,
    values='Revenue',
    index='Department',
    columns='Quarter',
    aggfunc='sum',
    margins=True,
    margins_name='Total'
)

# 写入 Excel 并设置格式
pivot.to_excel(writer, sheet_name='Pivot')
```

## CSV 导入/导出

```python
# CSV 转 XLSX
df = pd.read_csv('data.csv', encoding='utf-8-sig')
df.to_excel('output.xlsx', index=False)

# XLSX 转 CSV
df = pd.read_excel('data.xlsx')
df.to_csv('output.csv', index=False, encoding='utf-8-sig')

# 处理编码问题
df = pd.read_csv('data.csv', encoding='latin-1')  # 或 'cp1252'
```

## 大文件处理

### 内存高效读取

```python
# openpyxl 只读模式
from openpyxl import load_workbook

wb = load_workbook('large_file.xlsx', read_only=True)
ws = wb.active

for row in ws.iter_rows(min_row=2, values_only=True):
    process_row(row)

wb.close()
```

### 分块写入

```python
# 分块写入大数据集
from openpyxl import Workbook
from openpyxl.utils.dataframe import dataframe_to_rows

wb = Workbook(write_only=True)
ws = wb.create_sheet()

# 写入表头
ws.append(headers)

# 分块写入
chunk_size = 10000
for chunk in pd.read_csv('large.csv', chunksize=chunk_size):
    for row in dataframe_to_rows(chunk, index=False, header=False):
        ws.append(row)

wb.save('output.xlsx')
```

### 性能决策表

| 行数           | 策略                                  | 说明                      |
| -------------- | ------------------------------------- | ------------------------- |
| < 10,000       | 标准 openpyxl 或 pandas               | 可使用完整格式            |
| 1 万 - 10 万   | write_only / read_only 模式，分块处理 | write_only 模式下格式有限 |
| 10 万 - 100 万 | write_only 模式，考虑改用 CSV         | 接近 Excel 行数限制       |
| > 100 万       | 使用 CSV 或 Parquet，不用 XLSX        | Excel 限制：1,048,576 行  |

## 反模式 / 常见错误

| 反模式                   | 失败原因                 | 正确做法                                      |
| ------------------------ | ------------------------ | --------------------------------------------- |
| 用 openpyxl 做纯数据分析 | 分析操作冗长且慢         | 使用 pandas 做数据操作                        |
| 将大文件加载到内存       | 内存耗尽、崩溃           | 使用 read_only / write_only 模式              |
| 硬编码行号/列号          | 数据形状变化时代码失效   | 根据数据长度计算                              |
| 日期格式不一致           | 日期显示为数字或字符串   | 显式设置 number_format                        |
| 不关闭 read_only 工作簿  | 资源泄漏                 | 始终调用 `wb.close()` 或使用上下文管理器      |
| 使用 .xls 格式           | 过时、有限制、有安全风险 | 始终使用 .xlsx                                |
| 逐个单元格设置格式       | 大范围时极慢             | 对区域应用样式或使用命名样式                  |
| 未在实际 Excel 中测试    | 功能渲染效果不同         | 在 Excel、LibreOffice 和 Google Sheets 中测试 |
| 忘记冻结表头行           | 滚动大数据时用户体验差   | 数据工作表始终冻结窗格                        |

## 反合理化防护

- 不要用 openpyxl 做 pandas 一行就能完成的数据分析。
- 不要跳过行数评估 — 它决定了你的整个方案。
- 不要以为标准模式能处理超过 1 万行的文件 — 使用流式模式。
- 不要只在一个电子表格应用中测试 — 格式渲染有差异。
- 不要忘记关闭以 read_only 模式打开的工作簿。

## 集成点

| 技能                     | 连接方式                      |
| ------------------------ | ----------------------------- |
| `pdf-processing`         | Excel 数据输入到 PDF 报表生成 |
| `docx-processing`        | Excel 数据填充 Word 文档表格  |
| `file-organizer`         | 输出文件命名和目录结构规范    |
| `database-schema-design` | 数据库导出到 Excel 用于报表   |
| `deployment`             | CI/CD 流水线中自动生成报表    |

## 技能类型

**灵活（FLEXIBLE）** — 富格式使用 openpyxl，数据分析使用 pandas。当需要基于数据分析生成带格式报表时，结合两者。根据数据量调整文件处理策略。
