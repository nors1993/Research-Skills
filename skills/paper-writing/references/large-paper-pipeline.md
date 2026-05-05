# Large-Paper Generation Pipeline (Python → JSON → JS → docx)

When generating papers >10KB of content (15+ pages), building the JS generator
directly from inline strings is fragile. Chinese quotes, long paragraphs, and
nested escaping cause hard-to-diagnose `SyntaxError`s.

## Proven Pipeline

1. **Write content in Python** — store all sections as a dict in memory
2. **Serialize to JSON** — `json.dump(content, ensure_ascii=False)` to a temp file
3. **Read JSON in Python, build JS** — iterate sections, classify by heading pattern,
   apply correct escape function per context
4. **Run `node gen_paper.js`** — produces the `.docx`

## Escape Functions

```python
def js_str_for_backtick(s):
    """For template literals: bodyP(`...`)"""
    return s.replace('\\', '\\\\').replace('`', '\\`').replace('${', '\\${')

def js_str_for_quotes(s):
    """For double-quoted strings: new TextRun({ text: "..." })"""
    return s.replace('\\', '\\\\').replace('"', '\\"').replace('\n', '\\n')
```

## Heading Detection (Chinese paper convention)

```python
if p.startswith('摘要') or p.startswith('关键词'):
    js = f'boldP(`{escaped}`)'
elif re.match(r'^[2-6] ', p):  # Chapter headings: "2 全球..."
    js = f'new Paragraph({{ heading: HeadingLevel.HEADING_1, ... }})'
elif re.match(r'^[1-6]\.', p):  # Sub-headings: "2.1 ..."
    js = f'new Paragraph({{ heading: HeadingLevel.HEADING_2, ... }})'
else:
    js = f'bodyP(`{escaped}`)'
```

## Table Generation

Build tables as 2D arrays in Python, then generate JS `Table` code.
Always set `columnWidths` array matching cell `width` values.
Use `ShadingType.CLEAR` (never SOLID). Row 0 gets header shade `"D5E8F0"`.

## Reference List

References go into `new TextRun({ text: "[n] citation text", ... })` —
use `js_str_for_quotes()`, not backtick escaping. Sort alphabetically by
author surname, then by publication year.

## Cleanup After Generation

Remove `.py`, `.js`, `.json` temp files and `node_modules/` directory.
Keep only the three `.docx` deliverables.
