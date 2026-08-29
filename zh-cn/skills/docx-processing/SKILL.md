---
name: docx-processing
description: >
  当用户需要使用 python-docx 或 docxtpl 进行 Word 文档生成、模板填充、格式设置、
  邮件合并或 DOCX 操作时使用此技能。
  触发条件：以编程方式生成 Word 文档、用数据填充文档模板、应用格式（标题、表格、图片、页眉/页脚）、
  执行邮件合并操作、将 DOCX 转换为 PDF、管理文档样式。
---

# DOCX 文档处理

## 概述

以编程方式生成、操作和模板化 Word 文档。本技能涵盖使用 python-docx 进行直接文档创建、使用 docxtpl 进行基于 Jinja2 的模板填充、格式控制（标题、表格、图片、页眉/页脚）、邮件合并操作、样式管理以及转换策略。

当需要通过代码而非手动编辑来创建、填充或转换 Word 文档时，请应用此技能。

## 多阶段流程

### 阶段一：需求分析

1. 确定是从头创建还是填充模板
2. 识别文档结构（章节、页眉、表格、图片）
3. 定义数据源（JSON、CSV、数据库、API）
4. 规划样式需求（字体、颜色、页边距）
5. 确定输出格式（DOCX、是否需要转换为 PDF）

> **停止 — 在确定方法（从头创建 vs 模板填充）并确认数据源之前，切勿开始实现。**

### 阶段二：实现

1. 设置文档模板或从头创建
2. 实现数据绑定和内容生成
3. 应用格式和样式
4. 添加页眉、页脚和页码
5. 处理图片和嵌入对象

> **停止 — 在所有文档部分实现完成之前，切勿跳转到验证阶段。**

### 阶段三：验证

1. 验证文档在 Word/LibreOffice 中是否正确渲染
2. 检查跨页面的格式一致性
3. 验证生成文档中的数据准确性
4. 使用边界情况测试（长文本、缺失数据、特殊字符）
5. 如需转换，验证 PDF 输出

## 方法决策表

| 场景                     | 方法        | 库                     | 原因                                 |
| ------------------------ | ----------- | ---------------------- | ------------------------------------ |
| 一次性报告生成           | 从头创建    | python-docx            | 完全的编程控制                       |
| 具有固定布局的周期性报告 | 模板        | docxtpl                | 在 Word 中设计布局，用数据填充       |
| 批量信函生成（邮件合并） | 模板        | docxtpl                | 一个模板，多个输出                   |
| 复杂格式、自定义样式     | 从头创建    | python-docx            | 直接访问文档模型                     |
| 非技术人员设计模板       | 模板        | docxtpl                | 用户在 Word 中编辑，开发人员绑定数据 |
| 需要 PDF 输出            | 任一 + 转换 | libreoffice / docx2pdf | 后处理步骤                           |

## python-docx 模式

### 文档创建

```python
from docx import Document
from docx.shared import Inches, Pt, Cm, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.enum.table import WD_TABLE_ALIGNMENT

doc = Document()

# 设置默认字体
style = doc.styles['Normal']
font = style.font
font.name = 'Calibri'
font.size = Pt(11)

# 添加标题
doc.add_heading('月度报告', level=0)

# 添加带格式的段落
para = doc.add_paragraph()
run = para.add_run('重要：')
run.bold = True
run.font.color.rgb = RGBColor(0xCC, 0x00, 0x00)
para.add_run('此部分需要特别注意。')

# 添加表格
table = doc.add_table(rows=1, cols=3, style='Light Grid Accent 1')
hdr_cells = table.rows[0].cells
hdr_cells[0].text = '姓名'
hdr_cells[1].text = '部门'
hdr_cells[2].text = '收入'

for name, dept, rev in data:
    row_cells = table.add_row().cells
    row_cells[0].text = name
    row_cells[1].text = dept
    row_cells[2].text = f'${rev:,.2f}'

# 添加图片
doc.add_picture('chart.png', width=Inches(5.5))

# 保存
doc.save('report.docx')
```

### 页眉和页脚

```python
from docx.enum.section import WD_ORIENT
from docx.oxml.ns import qn
from docx.oxml import OxmlElement

section = doc.sections[0]

# 页面设置
section.page_width = Cm(21)
section.page_height = Cm(29.7)
section.left_margin = Cm(2.5)
section.right_margin = Cm(2.5)
section.top_margin = Cm(2.5)
section.bottom_margin = Cm(2.5)

# 页眉
header = section.header
header_para = header.paragraphs[0]
header_para.text = '公司名称 — 机密'
header_para.alignment = WD_ALIGN_PARAGRAPH.RIGHT
header_para.style.font.size = Pt(9)
header_para.style.font.color.rgb = RGBColor(0x88, 0x88, 0x88)

# 带页码的页脚
footer = section.footer
footer_para = footer.paragraphs[0]
footer_para.alignment = WD_ALIGN_PARAGRAPH.CENTER

# 添加页码字段
run = footer_para.add_run()
fldChar = OxmlElement('w:fldChar')
fldChar.set(qn('w:fldCharType'), 'begin')
run._r.append(fldChar)

run2 = footer_para.add_run()
instrText = OxmlElement('w:instrText')
instrText.set(qn('xml:space'), 'preserve')
instrText.text = ' PAGE '
run2._r.append(instrText)

run3 = footer_para.add_run()
fldChar2 = OxmlElement('w:fldChar')
fldChar2.set(qn('w:fldCharType'), 'end')
run3._r.append(fldChar2)
```

### 表格格式设置

```python
from docx.shared import Cm, Pt
from docx.oxml.ns import nsdecls
from docx.oxml import parse_xml

# 设置列宽
table.columns[0].width = Cm(4)
table.columns[1].width = Cm(6)
table.columns[2].width = Cm(3)

# 单元格底纹
for cell in table.rows[0].cells:
    shading = parse_xml(f'<w:shd {nsdecls("w")} w:fill="2F5496"/>')
    cell._tc.get_or_add_tcPr().append(shading)
    for paragraph in cell.paragraphs:
        for run in paragraph.runs:
            run.font.color.rgb = RGBColor(0xFF, 0xFF, 0xFF)
            run.font.bold = True

# 单元格对齐
for row in table.rows:
    for cell in row.cells:
        cell.paragraphs[0].alignment = WD_ALIGN_PARAGRAPH.CENTER
```

## docxtpl 模板模式

### 模板语法（Jinja2）

```
模板文件（template.docx）包含：

{{ company_name }}
日期：{{ report_date }}

尊敬的 {{ recipient_name }}：

{% for item in items %}
- {{ item.name }}：${{ item.price }}
{% endfor %}

总计：${{ total }}

{%if urgent %}
紧急：此事项需要立即处理。
{%endif %}
```

### 模板渲染

```python
from docxtpl import DocxTemplate, InlineImage
from docx.shared import Mm

tpl = DocxTemplate('template.docx')

context = {
    'company_name': 'Acme Corp',
    'report_date': '2025-03-15',
    'recipient_name': 'Alice Johnson',
    'items': [
        {'name': '小部件 A', 'price': '29.99'},
        {'name': '小部件 B', 'price': '49.99'},
    ],
    'total': '79.98',
    'urgent': True,
    'chart': InlineImage(tpl, 'chart.png', width=Mm(120)),
}

tpl.render(context)
tpl.save('output.docx')
```

### 模板中的富文本

```python
from docxtpl import RichText

rt = RichText()
rt.add('普通文本 ')
rt.add('粗体文本', bold=True)
rt.add(' 和 ')
rt.add('红色文本', color='FF0000')
rt.add(' 带 ')
rt.add('链接', url_id=tpl.build_url_id('https://example.com'))

context = {'formatted_text': rt}
```

### 模板中的表格

```
模板表格行带循环：
{% tr for row in table_data %}
{{ row.name }} | {{ row.value }} | {{ row.status }}
{% endtr %}
```

## 邮件合并

```python
from docxtpl import DocxTemplate
import csv

template = DocxTemplate('letter_template.docx')

with open('recipients.csv') as f:
    reader = csv.DictReader(f)
    for i, row in enumerate(reader):
        context = {
            'name': row['name'],
            'address': row['address'],
            'amount': row['amount'],
            'due_date': row['due_date'],
        }
        template.render(context)
        template.save(f'letters/letter_{i:04d}_{row["name"]}.docx')
        template = DocxTemplate('letter_template.docx')  # 为下一次迭代重新加载
```

## 样式管理

### 自定义样式

```python
from docx.enum.style import WD_STYLE_TYPE

# 创建自定义段落样式
style = doc.styles.add_style('CustomHeading', WD_STYLE_TYPE.PARAGRAPH)
style.font.name = 'Arial'
style.font.size = Pt(16)
style.font.bold = True
style.font.color.rgb = RGBColor(0x2F, 0x54, 0x96)
style.paragraph_format.space_before = Pt(12)
style.paragraph_format.space_after = Pt(6)

# 应用自定义样式
doc.add_paragraph('章节标题', style='CustomHeading')
```

### 样式继承

```
正文 → 标题 1 → 标题 2 → ...
正文 → 正文文本 → 列表段落
正文 → 表格正文 → 表格网格
```

## 转换策略

### DOCX 转 PDF

```python
# 选项 1：LibreOffice（最可靠，适合服务器环境）
import subprocess
subprocess.run([
    'libreoffice', '--headless', '--convert-to', 'pdf',
    '--outdir', output_dir, input_file
])

# 选项 2：docx2pdf（Windows/macOS 且已安装 Word）
from docx2pdf import convert
convert('input.docx', 'output.pdf')

# 选项 3：使用 reportlab 直接生成 PDF 以获得完全控制
```

## 错误处理

```python
import jinja2

def safe_generate_document(template_path, context, output_path):
    try:
        tpl = DocxTemplate(template_path)
        tpl.render(context)
        tpl.save(output_path)
        return True
    except jinja2.UndefinedError as e:
        print(f"模板变量缺失：{e}")
        return False
    except FileNotFoundError as e:
        print(f"模板未找到：{e}")
        return False
    except Exception as e:
        print(f"文档生成失败：{e}")
        return False
```

## 反模式 / 常见错误

| 反模式                       | 失败原因               | 替代方案                                     |
| ---------------------------- | ---------------------- | -------------------------------------------- |
| 硬编码字体大小而非使用样式   | 格式不一致，难以维护   | 定义一次样式，处处应用                       |
| 不处理缺失的模板变量         | 数据不完整时运行时崩溃 | 使用 `jinja2.Undefined` 或默认过滤器         |
| 无分页的大型表格             | 输出不可读，布局损坏   | 跨页拆分表格或进行摘要                       |
| 绝对图片路径                 | 跨环境时便携性差       | 使用相对路径或嵌入图片                       |
| 未在不同 Word 版本中测试     | 格式静默损坏           | 在 Word、LibreOffice 和 Google Docs 中测试   |
| 当存在 API 时直接修改 XML    | 代码脆弱、依赖版本     | 优先使用 python-docx API 方法                |
| 全部使用直接格式，不使用样式 | 无法保持一致性         | 创建并应用命名样式                           |
| 忽略 Unicode 字符            | 生成文档出现乱码       | 使用带重音字符、中文/日文/韩文、符号进行测试 |
| 邮件合并中不重新加载模板     | 首次渲染后输出损坏     | 每次迭代重新实例化 DocxTemplate              |

## 反合理化防护

- 切勿跳过方法决策（从头创建 vs 模板填充）— 这决定了你的整个实现方案。
- 切勿在未至少在 Word 和一个替代查看器中测试的情况下生成文档。
- 切勿忽略缺失数据 — 使用默认值或条件部分处理空/空值字段。
- 切勿在生产环境的文档生成流程中跳过错误处理。
- 当可以使用样式时，切勿硬编码格式。

## 集成点

| 技能                      | 连接方式                             |
| ------------------------- | ------------------------------------ |
| `pdf-processing`          | DOCX 转 PDF 转换，或直接选择生成 PDF |
| `xlsx-processing`         | Excel 数据作为文档生成上下文的输入   |
| `content-research-writer` | 研究内容格式化为白皮书和报告         |
| `file-organizer`          | 输出文件命名和目录结构约定           |
| `deployment`              | CI/CD 或服务器环境中的文档生成流程   |

## 技能类型

**灵活** — 根据文档复杂度在 python-docx（编程式）和 docxtpl（模板式）之间选择。简单报告可能不需要模板；复杂的周期性文档则受益于模板。
