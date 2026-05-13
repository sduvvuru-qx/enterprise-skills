---
name: Astemo XLSX
description: "Use when creating, styling, updating, or validating Astemo-branded Excel workbooks (.xlsx/.xlsm/.csv-derived trackers), including change point trackers, DFMEA, DRBFM, quality schedules, project schedules, status workbooks, and openpyxl-based spreadsheet generation."
icon: icon.svg
metadata:
  filePattern: '**/*.xlsx'
  bashPattern: openpyxl|xlsx
  priority: 80
  category: enterprise/astemo
  family: design-system
  lifecycle: active
  canonical_slug: astemo-xlsx
  icon_style: craft-category-glyph-v1
  design_source: astemo-design-system
---

# Astemo XLSX — Thin Wrapper

## Redirect

Load `astemo-design-system` first. It is the source of truth for:

- Astemo red `#B6001A` (never `#C00000`)
- Arial-first typography
- XLSX header, border, status-fill, and tab-color tokens
- Cross-format shell discipline and invisible-infrastructure rules

This skill only adds Excel/openpyxl execution rules.

## Execution rules

1. Preserve existing workbook formulas, hidden sheets, merged-cell structure, named ranges, filters, and print settings unless the operator asks to change them.
2. Apply Astemo styling only to visible work ranges; do not add Shadow/Craft/agent metadata, hidden provenance sheets, or debug properties.
3. Save durable workbooks to the project/program output path or the confirmed Dropbox program folder; do not leave the only copy in `/tmp`.
4. For Astemo finals, prefer program Dropbox routing over ad-hoc Google Drive upload unless the operator explicitly requests Google Drive.
5. Preserve Japanese text exactly. Do not translate or normalize JP cell values unless requested.

## Minimal openpyxl pattern

```python
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side

ASTEMO = {
    "hdr_fill": PatternFill("solid", fgColor="B6001A"),
    "hdr_font": Font(name="Arial", bold=True, color="FFFFFF", size=11),
    "data_font": Font(name="Arial", size=10, color="232323"),
    "alt_fill": PatternFill("solid", fgColor="F2F2F2"),
    "border": Border(
        left=Side(style="thin", color="D9D9D9"),
        right=Side(style="thin", color="D9D9D9"),
        top=Side(style="thin", color="D9D9D9"),
        bottom=Side(style="thin", color="D9D9D9"),
    ),
    "center": Alignment(horizontal="center", vertical="center", wrap_text=True),
    "wrap": Alignment(vertical="top", wrap_text=True),
    "complete": PatternFill("solid", fgColor="C6EFCE"),
    "in_progress": PatternFill("solid", fgColor="FFEB9C"),
    "open": PatternFill("solid", fgColor="FFC7CE"),
    "not_started": PatternFill("solid", fgColor="D9D9D9"),
    "target": PatternFill("solid", fgColor="BDD7EE"),
}
```

## Validation checklist

- Header fill is `#B6001A`; no `#C00000`.
- Fonts specify `Arial`.
- Alternating rows use `#FFFFFF` / `#F2F2F2`.
- Status columns use text labels plus fills; color alone does not carry meaning.
- Header row is frozen and filtered when workbook is a tracker.
- Landscape print orientation is set for wide trackers.
- Output path is durable and reported once.
