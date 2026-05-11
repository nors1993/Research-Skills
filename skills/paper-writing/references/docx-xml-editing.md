# 中文 .docx 文本替换：XML 直接编辑方案

## 问题

当编辑含大量中文文本的 .docx 文件时，使用 python-docx 的 `paragraph.text` 或 `run.text` 替换常常失败，原因有二：

1. **中文文本被拆分到多个 `<w:r>` 元素中**：Word 处理器（尤其是 WPS/LibreOffice）在保存 .docx 时，会将中文和英文之间的切换标记为独立的 run，导致一个完整的中文句子被切成数十个 `<w:r>` 片段
2. **`paragraph.text` 是只读拼接**：虽然 `.text` 能读出完整内容，但不能直接赋值修改

## 方案：Unpack → XML 直接编辑 → Repack

这是处理中文 .docx 最可靠的方法：

```python
import zipfile, tempfile, shutil, os, re

SRC = 'paper.docx'
OUT = 'paper_fixed.docx'
tmpdir = tempfile.mkdtemp()

# Step 1: Unpack
with zipfile.ZipFile(SRC, 'r') as z:
    z.extractall(tmpdir)

# Step 2: Edit document.xml
doc_xml = os.path.join(tmpdir, 'word', 'document.xml')
with open(doc_xml, 'r', encoding='utf-8') as f:
    xml = f.read()

# Apply replacements — use XML-escaped forms
xml = xml.replace('Old text &amp; citation', 'New text &amp; citation')

with open(doc_xml, 'w', encoding='utf-8') as f:
    f.write(xml)

# Step 3: (Optional) Replace images
# shutil.copy2('new_image.png', os.path.join(tmpdir, 'word/media/image5.png'))

# Step 4: Repack
with zipfile.ZipFile(OUT, 'w', zipfile.ZIP_DEFLATED) as zout:
    for root, dirs, files in os.walk(tmpdir):
        for file in files:
            full = os.path.join(root, file)
            arcname = os.path.relpath(full, tmpdir)
            zout.write(full, arcname)

shutil.rmtree(tmpdir)
```

## 关键注意事项

### XML 转义
docx 内部 XML 使用标准实体：
- `&` → `&amp;`
- `—` (em dash) → `\u2013` 或 `\u2014`
- 中文引号 `""` → 保留原 Unicode，无需额外转义

### 替换策略优先级
1. **整个 `<w:r>` 内容替换**：如果原文出现在单个 `<w:t>` 中，用 `str.replace()` 最简便
2. **跨 run 替换**：如果目标文本被切分在多个 `<w:t>` 中，使用 `re.sub()` 跨 XML 标记匹配
3. **整段替换**：对于参考文献条目（每个 `<w:p>` 仅含一条文献），重建整个 `<w:p>` 是最干净的方案

### 整段替换函数
```python
def replace_para_text(xml_str, search_text, new_text):
    """Find paragraph containing search_text and replace its content entirely."""
    idx = xml_str.find(search_text)
    if idx < 0:
        return xml_str, False
    
    p_start = xml_str.rfind('<w:p ', 0, idx)
    if p_start < 0:
        p_start = xml_str.rfind('<w:p>', 0, idx)
    p_end = xml_str.find('</w:p>', idx) + len('</w:p>')
    
    old_para = xml_str[p_start:p_end]
    
    # Preserve paragraph properties
    ppr_match = re.search(r'(<w:pPr>.*?</w:pPr>)', old_para, re.DOTALL)
    ppr = ppr_match.group(1) if ppr_match else ''
    
    # Preserve run properties from first run
    rpr_match = re.search(r'<w:r>(<w:rPr>.*?</w:rPr>)', old_para, re.DOTALL)
    rpr = rpr_match.group(1) if rpr_match else '<w:rPr></w:rPr>'
    
    escaped = new_text.replace('&', '&amp;').replace('<', '&lt;').replace('>', '&gt;')
    new_para = f'<w:p>{ppr}<w:r>{rpr}<w:t xml:space="preserve">{escaped}</w:t></w:r></w:p>'
    
    return xml_str[:p_start] + new_para + xml_str[p_end:], True
```

### 替换图片并调整尺寸
```python
# Replace image file
shutil.copy2(new_png, os.path.join(tmpdir, 'word/media/image5.png'))

# Update extent in document.xml
target_w_emu = int(6.0 * 914400)  # 6 inches width
target_h_emu = int(target_w_emu * img_h / img_w)  # maintain aspect ratio

# Find and update the wp:extent for the target image
rid_pos = xml.find('rId8')  # target image relationship ID
if rid_pos > 0:
    after = xml[rid_pos:]
    m = re.search(r'<wp:extent\s+cx="(\d+)"\s+cy="(\d+)"', after)
    if m:
        old_extent = m.group(0)
        new_extent = f'<wp:extent cx="{target_w_emu}" cy="{target_h_emu}"'
        xml = xml.replace(old_extent, new_extent)
```

## 验证
替换后务必验证：
```python
from docx import Document
doc = Document(OUT)
for p in doc.paragraphs:
    if 'TargetText' in p.text:
        print(f'OK: ...{p.text[:100]}...')
```
