# Bilingual (EN+ZH) Paralleled Document Generation

## When to use
User requests English source documents with Chinese translations in parallel format (paragraph-by-paragraph or cell-by-cell).

## Technique

### Reading the source
```python
from docx import Document
doc = Document('source.docx')
paragraphs = [p.text for p in doc.paragraphs if p.text.strip()]
```

### Structuring output
For each English paragraph, insert a Chinese translation immediately after it:
- English paragraph → original formatting
- Chinese paragraph → same formatting + dark blue color (`#00008B`) + `【中文】` prefix

### Preferred approach: delegate_task for parallelism
When translating multiple documents, use `delegate_task` with one sub-agent per document:
```python
delegate_task(tasks=[
    {"context": "Read SOURCE.docx... translate to Chinese... save BILINGUAL.docx", 
     "goal": "Create bilingual EN+ZH version of SOURCE.docx",
     "toolsets": ["terminal", "file"]},
    # ... more documents
])
```
Each sub-agent independently reads, translates, and writes its document. This avoids sequential bottlenecks on large files.

### Table handling
For tables, expand each row into two: original EN row + ZH translation row immediately below. Preserve header formatting (bold, blue background). Use `【中文】` prefix on all ZH cells.

### Translation method
- Google Translate via `deep-translator` library: `pip install deep-translator` then `from deep_translator import GoogleTranslator`
- Batch texts into groups of 20-30 to avoid rate limits
- Split paragraphs >4000 chars at sentence boundaries before translating

### Font note
Chinese translations use `SimSun` (宋体) as the east-Asian font; English uses `Times New Roman`. Set both via:
```python
run.font.name = 'Times New Roman'
run.element.rPr.rFonts.set(qn('w:eastAsia'), 'SimSun')
```

## Files generated
- `*_中英对照.docx` — bilingual output
- `build_bilingual.py` — reusable generation script (delete after use unless user asks to keep)
