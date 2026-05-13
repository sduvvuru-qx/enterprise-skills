---
name: Astemo Template Fallback
description: 'Use when the user needs: Fallback template strategy when canonical Astemo templates are offline. Copy branded files as template bases. Use this for template offline, reuse branded file, or no template available. Primary: astemo-org-templates skill.'
icon: icon.svg
type: implementation
metadata:
  depends_on: [astemo-org-templates]
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-template-fallback
  icon_style: craft-category-glyph-v1
---

# astemo-template-fallback

**Fallback strategy** when canonical `astemo-org-templates` are offline.

## Template Resolution Order

1. **Canonical system** — `astemo-org-templates` skill (23 files, 7 teams, build system)
2. **Local folder** — `~/Templates/Astemo/` (pre-populated, always available)
3. **Cloud** — Google Drive / SharePoint canonical templates
4. **Any branded file** — any `.pptx`, `.xlsx`, or `.docx` on disk with Astemo branding

## Local Templates Folder

Path: `~/Templates/Astemo/`

```
~/Templates/Astemo/
  pptx/
    staff-meeting-report.pptx      — xEV Inverter SOP template (best PPTX base)
    mlc-response.pptx              — Honda MLC format
  xlsx/
    tracker-template.xlsx           — Change point tracker base
    schedule-template.xlsx          — DE Activity schedule base
  docx/
    memo-template.docx              — Internal memo base
```

**Population:** Proactively copy good branded files here when first encountered. Do not wait until needed — cloud may be offline at that point.

## Pattern (all file types)

```
find branded file → copy → clear content → edit over it → save
```

Key: `shutil.copy2()` before opening. Never create from blank — you lose all branding (slide master, styles, headers, footers, brand chrome).

### PPTX

```python
import shutil
from pptx import Presentation

shutil.copy2(SOURCE, OUTPUT)
prs = Presentation(OUTPUT)

# Inspect layouts
for i, layout in enumerate(prs.slide_layouts):
    print(f"  [{i}] {layout.name}")

# Clear content, keep structure
for slide in prs.slides:
    for shape in slide.shapes:
        if shape.has_text_frame and hasattr(shape, 'placeholder_format') and shape.placeholder_format is not None:
            for para in shape.text_frame.paragraphs:
                for run in para.runs:
                    run.text = ""
        if shape.has_table:
            for row in shape.table.rows:
                for cell in row.cells:
                    cell.text = ""

prs.save(OUTPUT)
```

### XLSX

```python
import shutil
from openpyxl import load_workbook

shutil.copy2(SOURCE, OUTPUT)
wb = load_workbook(OUTPUT)

# Clear data, keep formatting/styles
for ws in wb.worksheets:
    for row in ws.iter_rows(min_row=2):  # keep row 1 headers
        for cell in row:
            cell.value = None

wb.save(OUTPUT)
```

### DOCX

```python
import shutil
from docx import Document

shutil.copy2(SOURCE, OUTPUT)
doc = Document(OUTPUT)

# Clear body, keep styles/headers/footers
for para in doc.paragraphs:
    for run in para.runs:
        run.text = ""
# Clear tables
for table in doc.tables:
    for row in table.rows:
        for cell in row.cells:
            for para in cell.paragraphs:
                for run in para.runs:
                    run.text = ""

doc.save(OUTPUT)
```

## Design Tokens (always preserve)

- Astemo Red: `#B6001A`
- Font: Arial
- PPTX slide size: 13.33" x 7.5" (widescreen)

## Key Rules

1. **Local folder first** — no cloud dependency
2. **Always copy before opening** — `shutil.copy2()` preserves master/slides/styles
3. **Never build from blank** — lose all branding
4. **Match layouts by substring** — names vary between source files
5. **Keep background elements** — brand bars, logos, footers are often empty-looking shapes
6. **This is fallback, not primary** — use canonical templates when available

## Relation to Other Skills

- **astemo-org-templates**: PRIMARY canonical system (23 files, 7 teams)
- **pptx-visual-validation**: Visual review via Quick Look export
- **astemo-xlsx**: Astemo Excel design tokens and styling
- **pptx / xlsx / docx** (official): General file manipulation

## Repository

Source of truth: https://github.com/sduvvuru-qx/astemo-profile (skills/astemo-template-fallback/)
**Formerly**: `astemo-pptx-copy-reuse` (renamed for clarity 2026-04-20)
