---
name: pdf-processing
description: >
  当用户需要 PDF 生成、操作、表单填充、表格提取、
  OCR 识别、合并、拆分、添加水印或元数据处理时使用。
  触发条件：生成 PDF 报告、从 PDF 中提取文本或表格、
  以编程方式填充 PDF 表单、合并或拆分 PDF 文件、添加水印、
  对扫描文档进行 OCR 识别、读取或写入 PDF 元数据、将 HTML 转换为 PDF。
---

# PDF 处理

## 概述

生成、操作并从 PDF 文档中提取数据。此技能涵盖 Python PDF 生态系统：pypdf 用于合并/拆分/元数据处理，pdfplumber 用于文本和表格提取，reportlab 用于文档生成，pytesseract 用于 OCR 识别，以及表单填充、水印添加和复杂文档组装的策略。

每当需要通过代码创建、解析、转换或组合 PDF 时，请应用此技能。

## 多阶段流程

### 阶段 1：需求分析

1. 确定操作类型（生成、提取、操作）
2. 识别输入 PDF 的特征（扫描版、电子版、表单）
3. 定义输出要求（格式、质量、大小）
4. 规划数据流水线（从源数据到 PDF 或从 PDF 到数据）
5. 评估数据量和性能需求

> **停止 — 在操作类型和输入特征明确之前，切勿选择库。**

### 阶段 2：实现

1. 为任务选择合适的库（参见决策表）
2. 实现核心处理逻辑
3. 处理边界情况（损坏的文件、加密的 PDF、混合内容）
4. 添加错误处理和验证
5. 针对文件大小和处理速度进行优化

> **停止 — 切勿跳过对加密、旋转或扫描 PDF 的边界情况处理。**

### 阶段 3：验证

1. 验证输出在多个 PDF 阅读器中是否正确渲染
2. 检查文本在适用情况下是否可选择（而非光栅化）
3. 验证提取数据的准确性
4. 使用边界情况 PDF 进行测试（大型、加密、扫描版）
5. 验证可访问性（需要时使用带标签的 PDF）

## 库选择决策表

| 任务            | 库                      | 原因                        | 替代方案                    |
| --------------- | ----------------------- | --------------------------- | --------------------------- |
| 文本提取        | pdfplumber              | 最佳准确性，处理布局能力强  | pypdf（更简单，准确性较低） |
| 表格提取        | pdfplumber              | 结构化表格解析              | camelot（专用表格工具）     |
| PDF 生成        | reportlab               | 完全控制，专业级质量        | weasyprint（HTML 转 PDF）   |
| 合并 / 拆分     | pypdf                   | 简单、可靠、快速            | —                           |
| 表单填充        | pypdf                   | 读取并填充 AcroForms        | pdfrw（替代 API）           |
| 元数据读取/写入 | pypdf                   | 读取/写入 PDF 属性          | —                           |
| OCR（扫描文档） | pytesseract + pdf2image | 扫描文档文本提取            | EasyOCR（深度学习）         |
| 添加水印        | pypdf + reportlab       | 页面叠加                    | —                           |
| HTML 转 PDF     | weasyprint              | 基于 CSS 的布局，服务器友好 | playwright（浏览器渲染）    |

## 使用 ReportLab 生成 PDF

```python
from reportlab.lib.pagesizes import A4
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.units import cm, mm
from reportlab.lib.colors import HexColor
from reportlab.platypus import (
    SimpleDocTemplate, Paragraph, Spacer, Table,
    TableStyle, Image, PageBreak
)
from reportlab.lib import colors

def generate_report(output_path, data):
    doc = SimpleDocTemplate(
        output_path,
        pagesize=A4,
        topMargin=2.5*cm,
        bottomMargin=2.5*cm,
        leftMargin=2.5*cm,
        rightMargin=2.5*cm,
    )

    styles = getSampleStyleSheet()
    styles.add(ParagraphStyle(
        name='CustomTitle',
        parent=styles['Title'],
        fontSize=24,
        textColor=HexColor('#2F5496'),
        spaceAfter=20,
    ))

    story = []

    # 标题
    story.append(Paragraph(data['title'], styles['CustomTitle']))
    story.append(Spacer(1, 12))

    # 正文文本
    story.append(Paragraph(data['body'], styles['Normal']))
    story.append(Spacer(1, 20))

    # 表格
    table_data = [['Name', 'Value', 'Status']]
    for row in data['rows']:
        table_data.append([row['name'], row['value'], row['status']])

    table = Table(table_data, colWidths=[6*cm, 4*cm, 4*cm])
    table.setStyle(TableStyle([
        ('BACKGROUND', (0, 0), (-1, 0), HexColor('#2F5496')),
        ('TEXTCOLOR', (0, 0), (-1, 0), colors.white),
        ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
        ('FONTSIZE', (0, 0), (-1, 0), 11),
        ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
        ('GRID', (0, 0), (-1, -1), 0.5, colors.grey),
        ('ROWBACKGROUNDS', (0, 1), (-1, -1), [colors.white, HexColor('#F0F4FA')]),
        ('TOPPADDING', (0, 0), (-1, -1), 8),
        ('BOTTOMPADDING', (0, 0), (-1, -1), 8),
    ]))
    story.append(table)

    doc.build(story)
```

### 自定义页面模板（页眉/页脚）

```python
from reportlab.platypus import BaseDocTemplate, Frame, PageTemplate
from datetime import datetime

def add_header_footer(canvas, doc):
    canvas.saveState()
    # 页眉
    canvas.setFont('Helvetica', 9)
    canvas.setFillColor(HexColor('#888888'))
    canvas.drawString(2.5*cm, A4[1] - 1.5*cm, 'Company Name — Confidential')
    canvas.drawRightString(A4[0] - 2.5*cm, A4[1] - 1.5*cm, f'Page {doc.page}')
    # 页脚
    canvas.drawCentredString(A4[0]/2, 1.5*cm, f'Generated on {datetime.now():%Y-%m-%d}')
    canvas.restoreState()

doc = BaseDocTemplate(output_path, pagesize=A4)
frame = Frame(2.5*cm, 2.5*cm, A4[0]-5*cm, A4[1]-5*cm)
doc.addPageTemplates([PageTemplate(id='main', frames=[frame], onPage=add_header_footer)])
```

## 文本和表格提取

### pdfplumber

```python
import pdfplumber

with pdfplumber.open('document.pdf') as pdf:
    # 从所有页面提取文本
    full_text = ''
    for page in pdf.pages:
        full_text += page.extract_text() + '\n'

    # 提取表格
    for page in pdf.pages:
        tables = page.extract_tables()
        for table in tables:
            for row in table:
                print(row)

    # 从特定区域提取文本
    page = pdf.pages[0]
    bbox = (50, 100, 400, 300)  # (x0, top, x1, bottom)
    cropped = page.within_bbox(bbox)
    text = cropped.extract_text()
```

### 表格提取设置

```python
table_settings = {
    "vertical_strategy": "lines",    # 或 "text", "explicit"
    "horizontal_strategy": "lines",
    "snap_tolerance": 3,
    "join_tolerance": 3,
    "edge_min_length": 3,
    "min_words_vertical": 3,
    "min_words_horizontal": 1,
}

tables = page.extract_tables(table_settings)
```

## 表单填充

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader('form.pdf')
writer = PdfWriter()
writer.append(reader)

# 填充表单字段
writer.update_page_form_field_values(
    writer.pages[0],
    {
        'full_name': 'Alice Johnson',
        'email': 'alice@example.com',
        'date': '2025-03-15',
        'agree_terms': '/Yes',  # 复选框
    },
    auto_regenerate=False,
)

with open('filled_form.pdf', 'wb') as f:
    writer.write(f)
```

## OCR（扫描版 PDF）

```python
from pdf2image import convert_from_path
import pytesseract

def ocr_pdf(pdf_path, language='eng'):
    images = convert_from_path(pdf_path, dpi=300)
    full_text = ''
    for i, image in enumerate(images):
        text = pytesseract.image_to_string(image, lang=language)
        full_text += f'\n--- Page {i+1} ---\n{text}'
    return full_text

# 针对特定布局提高准确性的方法：
def ocr_with_config(image):
    custom_config = r'--oem 3 --psm 6'  # LSTM 引擎，假设统一区块
    return pytesseract.image_to_string(image, config=custom_config)
```

## 合并与拆分

```python
from pypdf import PdfReader, PdfWriter

# 合并多个 PDF
def merge_pdfs(input_paths, output_path):
    writer = PdfWriter()
    for path in input_paths:
        reader = PdfReader(path)
        for page in reader.pages:
            writer.add_page(page)
    with open(output_path, 'wb') as f:
        writer.write(f)

# 按页面范围拆分 PDF
def split_pdf(input_path, ranges, output_dir):
    reader = PdfReader(input_path)
    for i, (start, end) in enumerate(ranges):
        writer = PdfWriter()
        for page_num in range(start - 1, min(end, len(reader.pages))):
            writer.add_page(reader.pages[page_num])
        with open(f'{output_dir}/part_{i+1}.pdf', 'wb') as f:
            writer.write(f)

# 提取特定页面
def extract_pages(input_path, page_numbers, output_path):
    reader = PdfReader(input_path)
    writer = PdfWriter()
    for num in page_numbers:
        writer.add_page(reader.pages[num - 1])
    with open(output_path, 'wb') as f:
        writer.write(f)
```

## 添加水印

```python
from pypdf import PdfReader, PdfWriter
from reportlab.pdfgen import canvas as rl_canvas
from reportlab.lib.pagesizes import A4
from io import BytesIO

def create_watermark(text, opacity=0.1):
    buffer = BytesIO()
    c = rl_canvas.Canvas(buffer, pagesize=A4)
    c.setFillAlpha(opacity)
    c.setFont('Helvetica-Bold', 60)
    c.setFillColorRGB(0.5, 0.5, 0.5)
    c.translate(A4[0]/2, A4[1]/2)
    c.rotate(45)
    c.drawCentredString(0, 0, text)
    c.save()
    buffer.seek(0)
    return PdfReader(buffer)

def apply_watermark(input_path, output_path, watermark_text):
    watermark = create_watermark(watermark_text)
    reader = PdfReader(input_path)
    writer = PdfWriter()

    for page in reader.pages:
        page.merge_page(watermark.pages[0])
        writer.add_page(page)

    with open(output_path, 'wb') as f:
        writer.write(f)
```

## 元数据处理

```python
from pypdf import PdfReader, PdfWriter

# 读取元数据
reader = PdfReader('document.pdf')
info = reader.metadata
print(f'Title: {info.title}')
print(f'Author: {info.author}')
print(f'Pages: {len(reader.pages)}')

# 写入元数据
writer = PdfWriter()
writer.append(reader)
writer.add_metadata({
    '/Title': 'Updated Title',
    '/Author': 'Author Name',
    '/Subject': 'Document Subject',
    '/Creator': 'My Application',
})
with open('updated.pdf', 'wb') as f:
    writer.write(f)
```

## 反模式 / 常见错误

| 反模式                             | 失败原因                         | 替代做法                                  |
| ---------------------------------- | -------------------------------- | ----------------------------------------- |
| 对电子版（基于文本的）PDF 使用 OCR | 当文本已可提取时，速度慢且不准确 | 先检查是否可提取文本，仅当为空时使用 OCR  |
| 未处理加密 PDF                     | 崩溃或静默失败                   | 检测加密，提示输入密码或优雅跳过          |
| 将整个大型 PDF 加载到内存          | 服务器上内存耗尽                 | 流式处理页面或分块处理                    |
| 忽略页面旋转元数据                 | 文本提取返回乱码结果             | 提取前读取并应用旋转                      |
| 硬编码页面尺寸                     | 在非 A4 文档上失效               | 从源 PDF 读取尺寸                         |
| 未关闭文件句柄                     | 长期运行进程中资源泄漏           | 使用上下文管理器（`with` 语句）           |
| 生成后未在多阅读器中测试           | 不同阅读器渲染效果不同           | 在 Adobe Reader、Preview 和 Chrome 中测试 |
| 提取表格时未调整设置               | 列对齐差、单元格合并错误         | 根据文档类型调整 `table_settings`         |

## 反合理化防护

- 切勿在未先尝试直接文本提取的情况下使用 OCR — 先检查 PDF 类型。
- 切勿跳过加密检测 — 即使"大多数 PDF 未加密"也要显式处理。
- 切勿假设页面尺寸为 A4 — 从源文档读取尺寸。
- 切勿仅在一个 PDF 阅读器中测试 — Adobe、Preview 和 Chrome 的渲染效果各不相同。
- 切勿在不采用内存优化模式（流式、分块）的情况下处理大型 PDF。

## 集成点

| 技能                      | 连接方式                                   |
| ------------------------- | ------------------------------------------ |
| `docx-processing`         | DOCX 转 PDF 转换流水线，或在格式间进行选择 |
| `xlsx-processing`         | Excel 数据填充 PDF 报告表格                |
| `content-research-writer` | 研究成果格式化为 PDF 白皮书                |
| `file-organizer`          | 输出文件命名和目录结构规范                 |
| `deployment`              | 服务器/CI 环境中的 PDF 生成流水线          |

## 技能类型

**灵活（FLEXIBLE）** — 根据具体 PDF 任务选择合适的库和方法。生成用 ReportLab，提取用 pdfplumber，操作用 pypdf。按需组合使用。
