---
name: markitdown-converter
description: Convert any document (PDF, DOCX, XLSX, PPTX, HTML, CSV, etc.) to Markdown using the markitdown CLI tool before reading.
---

# MarkItDown Converter

Before reading non-markdown files, always convert them to Markdown first using the `markitdown` tool.

## Installation

markitdown is installed at `/home/panxiandong/.local/bin/markitdown` and should be in PATH.

## Usage

```bash
# Convert a file to markdown and print to stdout
markitdown <file_path>

# Or use the alias
markitdown_convert <file_path>
```

## Supported Formats

| Format | Extension |
|--------|-----------|
| PDF | .pdf |
| Word | .docx |
| Excel | .xlsx, .xls |
| PowerPoint | .pptx |
| HTML | .html, .htm |
| CSV | .csv |
| JSON | .json |
| XML | .xml |
| EPUB | .epub |
| Outlook Message | .msg |
| RTF | .rtf |
| Jupyter Notebook | .ipynb |
| Plain Text | .txt |
| Audio (metadata) | .wav, .mp3 |
| Images (OCR) | .jpg, .jpeg, .png, .gif, .webp, .svg |
| Markdown (passthrough) | .md |

## Implementation

The tool is a Python wrapper script at `~/.local/bin/markitdown` that patches out the heavy `magika` ML dependency (onnxruntime was too large to download) with a lightweight file-extension + magic-bytes based detector. The core conversion uses Microsoft's `markitdown` Python package.

Venue: `~/.hermes/markitdown-env/`
