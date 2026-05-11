# docx 参考文献批量替换（Python zip + XML 法）

## 何时使用

论文优化阶段需要大量替换/修正参考文献（虚假文献替换、页码修正、作者名修正等），且 python-docx 的 run-level 操作因文本被拆分到多个 `<w:t>` 元素而不可靠时。

## 核心流程

```python
import zipfile, tempfile, os, shutil, re

tmpdir = tempfile.mkdtemp()
with zipfile.ZipFile('input.docx', 'r') as z:
    z.extractall(tmpdir)

doc_xml = os.path.join(tmpdir, 'word', 'document.xml')
with open(doc_xml, 'r', encoding='utf-8') as f:
    xml = f.read()

# 直接字符串替换（注意 XML 实体）
xml = xml.replace('Harrison, R. G., &amp; Cooper', 'Svotina, V., &amp; Cherkasova')

with open(doc_xml, 'w', encoding='utf-8') as f:
    f.write(xml)

# 替换图片
media = os.path.join(tmpdir, 'word', 'media')
shutil.copy2('new_figure.png', os.path.join(media, 'image5.png'))

# 调整图片尺寸（EMU 单位）
target_w_emu = int(6.0 * 914400)  # 6 inches
xml = re.sub(r'<wp:extent\s+cx="\d+"\s+cy="\d+"',
             f'<wp:extent cx="{target_w_emu}" cy="{target_h_emu}"', xml)

# 重新打包
with zipfile.ZipFile('output.docx', 'w', zipfile.ZIP_DEFLATED) as zout:
    for root, dirs, files in os.walk(tmpdir):
        for file in files:
            full = os.path.join(root, file)
            arcname = os.path.relpath(full, tmpdir)
            zout.write(full, arcname)
shutil.rmtree(tmpdir)
```

## 关键坑点

1. **XML 实体**：`&` 在 XML 中是 `&amp;`，`–` 是 `\u2013`，要精确匹配
2. **文本拆分**：中文段落常有多段 `<w:r>`/`<w:t>`，直接全文搜索替换比 run-level 操作可靠
3. **图片替换**：直接覆盖 media/ 目录下对应文件即可，无需改 relationship
4. **EMU 换算**：1 inch = 914400 EMU，图片宽度建议 6.0–6.5 inch（论文页宽）
5. **验证**：替换后用 python-docx 读回检查关键词是否存在
