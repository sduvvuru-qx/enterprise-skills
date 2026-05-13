# Astemo PDF Rendering CSS

Used with WeasyPrint or any HTML→PDF engine. Apply to the semantic payload converted to HTML.

```css
@page {
  size: letter;
  margin: 0.75in;
  @top-right {
    content: var(--doc-cls, "INTERNAL") "  |  Astemo, Ltd.";
    font-family: Arial, sans-serif;
    font-size: 8pt;
    color: #737373;
  }
  @bottom-center {
    content: "© Astemo, Ltd. All rights reserved.    Page " counter(page) " of " counter(pages);
    font-family: Arial, sans-serif;
    font-size: 8pt;
    color: #737373;
    border-top: 1px solid #D9D9D9;
    padding-top: 4pt;
  }
}

body { font-family: Arial, sans-serif; color: #232323; font-size: 11pt; line-height: 1.5; }
h1 { font-size: 16pt; color: #232323; font-weight: bold; border-bottom: 3px solid #B6001A; padding-bottom: 4pt; page-break-before: always; }
h1:first-of-type { page-break-before: avoid; }
h2 { font-size: 13pt; color: #4D4D4D; font-weight: bold; }
.title { font-size: 28pt; color: #B6001A; font-weight: bold; margin-bottom: 12pt; border-bottom: 3px solid #B6001A; padding-bottom: 6pt; }
table { width: 100%; border-collapse: collapse; margin-bottom: 14pt; font-size: 10pt; }
th { background: #F2F2F2; font-weight: bold; text-align: left; padding: 5px 8px; border: 1px solid #D9D9D9; }
td { padding: 4px 8px; border: 1px solid #D9D9D9; }
tr:nth-child(even) td { background: #F2F2F2; }
.metadata-table td:first-child { font-weight: bold; color: #4D4D4D; width: 25%; }
.st-complete { background: #C6EFCE !important; color: #497F37; font-weight: bold; }
.st-in-progress { background: #FFEB9C !important; color: #4D4D4D; font-weight: bold; }
.st-open { background: #FFC7CE !important; color: #B6001A; font-weight: bold; }
.st-not-started { background: #D9D9D9 !important; color: #737373; font-weight: bold; }
.st-high { background: #FFC7CE !important; color: #B6001A; font-weight: bold; }
.st-medium { background: #FFEB9C !important; color: #4D4D4D; font-weight: bold; }
.st-low { background: #D9D9D9 !important; color: #737373; font-weight: bold; }
.pri-p0 { color: #B6001A; font-weight: bold; }
.pri-p1 { color: #2A579A; font-weight: bold; }
.pri-p2 { color: #737373; font-weight: bold; }
pre { background: #F7F7F7; border: 1px solid #D9D9D9; padding: 8pt; font-family: Consolas, monospace; font-size: 9pt; page-break-inside: avoid; }
```

## reportlab equivalent colors

```python
from reportlab.lib.colors import HexColor

BRAND_RED = HexColor("#B6001A")
DARK_TEXT = HexColor("#232323")
TEXT_SECONDARY = HexColor("#4D4D4D")
TEXT_MUTED = HexColor("#737373")
RULE_LIGHT = HexColor("#D9D9D9")
WHITE = HexColor("#FFFFFF")
ROW_ALT = HexColor("#F2F2F2")
SURFACE_SOFT = HexColor("#F7F7F7")

STATUS_COMPLETE_BG = HexColor("#C6EFCE")
STATUS_COMPLETE_FG = HexColor("#497F37")
STATUS_IN_PROGRESS_BG = HexColor("#FFEB9C")
STATUS_IN_PROGRESS_FG = HexColor("#4D4D4D")
STATUS_OPEN_BG = HexColor("#FFC7CE")
STATUS_OPEN_FG = HexColor("#B6001A")
STATUS_NOT_STARTED_BG = HexColor("#D9D9D9")
STATUS_NOT_STARTED_FG = HexColor("#737373")
STATUS_TARGET_BG = HexColor("#BDD7EE")
STATUS_TARGET_FG = HexColor("#2A579A")
```
