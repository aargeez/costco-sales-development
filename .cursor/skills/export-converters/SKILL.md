---
name: export-converters
description: Convert Markdown, JSON, or HTML to PDF, DOCX, or HTML with tools/export/convert.py. Use whenever the user needs a PDF or Word file from an existing artifact — do not generate binary files in the model.
---

# Export converters

Run, do not invent bytes:

```bash
python tools/export/convert.py md2pdf <input.md> -o <output.pdf>
python tools/export/convert.py json2pdf <input.json> -o <output.pdf>
python tools/export/convert.py md2docx <input.md> -o <output.docx>
python tools/export/convert.py json2docx <input.json> -o <output.docx>
python tools/export/convert.py html2pdf <input.html> -o <output.pdf>
```

Install once: `pip install -r tools/export/requirements.txt`.

Mermaid code fences become flowchart PNGs (Playwright + Chrome/Chromium). Do not leave raw Mermaid source in Word/PDF.

`/application-summary` should write HTML first, then `html2pdf`. Never paste PDF hex into chat.
