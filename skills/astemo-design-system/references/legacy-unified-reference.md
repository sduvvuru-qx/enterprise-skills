---
name: Astemo Design System
description: "Unified Astemo design system — tokens, shell anatomy, brand truths, and per-format rules for PPTX, XLSX, DOCX, MD, HTML, Mermaid, and images. Load before generating any Astemo-branded artifact. Trigger on: Astemo, design system, Astemo template, branded artifact, Astemo colors, Astemo font, Astemo red, corporate template, any file creation for Astemo work."
metadata:
  category: enterprise/astemo
  family: design
  lifecycle: active
  canonical_slug: astemo-design-system
  icon_style: craft-category-glyph-v1
  replaces: [astemo-md-mmd-design-system, astemo-xlsx]
---

# Astemo Design System — Unified Reference

## Core Rule

```text
Astemo = visible brand. Always.
Shadow / Craft / agents = invisible infrastructure. Never in visible output.
```

### Visible-output ban list

Do **not** put these in visible Astemo body text, slide content, slide notes, filenames, captions, footers, Obsidian note bodies, Bases views, Canvas nodes, exported PDFs, or customer/internal work artifacts unless the artifact is explicitly an automation demo approved for that audience:

- `Shadow`, `Craft`, `agent`, `pi`, session IDs, tool names, relay internals, A2A internals, local runtime paths, hidden implementation hooks.
- If provenance is needed, place it in non-rendered frontmatter, sidecar manifests, or fenced internal metadata blocks; never in the visible brand shell.

## Two-Layer Model

Astemo artifacts are a **fixed shell** + **variable content**. Never improvise the shell.

| Layer | Behavior | Examples |
|-------|----------|----------|
| **Shell** | Highly fixed, highly disciplined | Logo, red rule, footer, white field, title stack |
| **Content** | Situational, variable density | Tables, charts, evidence panels, status legends |

**Acceptance test:** If the shell gets louder when content gets busier, generation failed.

## Token Table

### Colors

| Token | Hex | Use |
|-------|-----|-----|
| `brandRed` | `#B6001A` | Headers, rules, accents, logo default. NEVER use `#C00000`. |
| `darkText` | `#232323` | Primary body text |
| `textSecondary` | `#4D4D4D` | Gray 55% — secondary text, labels |
| `textMuted` | `#737373` | Gray 30% — captions, metadata |
| `ruleLight` | `#D9D9D9` | Gray 15% — thin borders, dividers |
| `ruleMid` | `#B3B3B3` | Gray 55% borders |
| `white` | `#FFFFFF` | Primary background |
| `rowAlt` | `#F2F2F2` | Alternating row fill |
| `surfaceSoft` | `#F7F7F7` | Subtle surface backgrounds |
| `info` | `#2A579A` | Informational status |
| `success` | `#497F37` | Complete, OK, on-track |
| `warning` | `#C6A647` | Caution, aging |
| `attention` | `#FF9B00` | Needs attention |
| `critical` | `#B6001A` | Same as brandRed — danger, overdue, blocked |
| `completeFill` | `#C6EFCE` | Status cell background: complete |
| `inProgressFill` | `#FFEB9C` | Status cell background: in progress |
| `openFill` | `#FFC7CE` | Status cell background: open/blocked |
| `notStartedFill` | `#D9D9D9` | Status cell background: not started |
| `targetFill` | `#BDD7EE` | Status cell background: target/on-plan |

### Theme Accent Palette (from PPTX theme)

| Accent | Hex | Typical use |
|--------|-----|-------------|
| accent1 | `#B6001A` | Brand red (same as brandRed) |
| accent2 | `#B3B3B3` | Neutral gray |
| accent3 | `#C6A647` | Warm gold |
| accent4 | `#FF9B00` | Orange attention |
| accent5 | `#92D050` | Green positive |
| accent6 | `#07A6EB` | Cyan/tech |
| hlink | `#0070C0` | Hyperlinks |

### Typography

| Token | Value |
|-------|-------|
| `fontPrimary` | Arial, Helvetica Neue, Helvetica, sans-serif |
| `fontCjk` | Yu Gothic UI, Yu Gothic, Meiryo UI, Meiryo |
| `fontMono` | SFMono-Regular, Consolas, Liberation Mono, Menlo, monospace |
| `titlePt` | 28 |
| `headingPt` | 20–24 |
| `bodyMinPt` | 16 |
| `captionPt` | 12 |

**Rules:**
- Arial first. No Calibri. No system default.
- Sentence case preferred. No underlined headings. No italic headings.
- 2–3 font sizes per artifact maximum.
- Japanese fallback: Yu Gothic UI or Meiryo UI.
- If Helvetica available (official materials): use Helvetica Bold for company name.

## Slide Master & Theme (Reverse-Engineered)

### Theme XML (theme1.xml)

```
Color scheme: "Custom 5"
  dk1 (dark1):    #232323  (primary text)
  lt1 (light1):   #FFFFFF  (background)
  dk2 (dark2):    #0070C0  (hyperlink blue)
  lt2 (light2):   #497F37  (success green)
  accent1:        #B6001A  (brand red)
  accent2:        #B3B3B3  (neutral gray)
  accent3:        #C6A647  (warm gold)
  accent4:        #FF9B00  (attention orange)
  accent5:        #92D050  (positive green)
  accent6:        #07A6EB  (cyan/tech)
  hlink:          #0070C0
  folHlink:       #B6001A

Font scheme: "Custom 1"
  majorFont latin: Arial
  majorFont ea:    Yu Gothic UI
  minorFont latin: Arial
  minorFont ea:    Yu Gothic UI
```

### Slide Master

The canonical template is **layout-driven**:

- `ppt/slideMasters/slideMaster1.xml` has **0 shapes**.
- Visible brand shell elements live in **slide layouts**, not on the master.
- The deck contains extra Office fallback theme files, but the Astemo source of truth is `ppt/theme/theme1.xml`.
- In `python-pptx`, **content standard slide = `slide_layouts[5]` / layout name `6. Standard Slide`**. Do **not** use `slide_layouts[6]`; that is `6_Last Slide`.

**Rule:** select layouts by **layout name** where possible and assert the resolved index before adding content. Never infer from the visible number in the layout name.

### Layout selection contract

| Task | Use layout name | `python-pptx` index | Notes |
|------|-----------------|--------------------:|-------|
| Title deck cover | `1. Title slide_1` | 0 | Default title shell |
| Visual title | `1_2. Title slide_2` or `2. Title slide_2` | 1 or 2 | Only when source image/visual belongs in title |
| Agenda / contents | `4. Contents` | 3 | Section list |
| Section divider | `5_Section slide` | 4 | Mid-deck divider |
| Standard content | `6. Standard Slide` | **5** | Primary content slide |
| Closing | `6_Last Slide` | 6 | Last slide only |
| Blank/no brand | `Blank slide (no brand)` | 7 | Avoid for Astemo work |
| Standard variant | `1_6. Standard Slide` | 8 | Content slide with title placeholder |

### Layout name `6. Standard Slide` — content standard (`python-pptx` index 5)

```
Slide: 13.33 × 7.5 in (16:9)

PICTURE: Astemo logo (image1.png, 84905 bytes, 3899×1921 px)
  Position: (11.36, 0.02) 1.76 × 0.87 in
  Role:     Identity anchor, top-right. NOT hero.

SHAPE: Text Placeholder 2 [ph=body idx=10]
  Position: (0.29, 0.20) 10.54 × 0.54 in
  Role:     Slide title placeholder

LINE: Red rule (直線コネクタ 16)
  Position: (0.0, 0.92) width=13.33 in
  Color:    #B6001A
  Width:    3pt (38100 EMU)
  Role:     Thin structural horizon separating shell from content

SHAPE: AutoShape 66 — CONFIDENTIAL badge
  Position: (6.03, 7.24) 1.27 × 0.16 in
  Geometry: roundRect
  Text:     "CONFIDENTIAL"
  Font:     Arial 9pt, color #9B2423
  Fill:     #9B2423
  Line:     #9B2423 0.2pt
  Role:     Mandatory confidentiality marker

SHAPE: TextBox 21 — Page number ‹#›
  Position: (12.75, 7.16) 0.44 × 0.34 in
  Text:     ‹#› (auto page number)
  Font:     14pt
  Role:     Page rhythm

SHAPE: TextBox 13 — Copyright
  Position: (10.56, 7.22) 2.12 × 0.25 in
  Text:     "© Astemo, Ltd. All rights reserved."
  Font:     Arial 9pt, theme color TEXT_1
  Role:     Persistent corporate footer
```

### Title Slide Layout (layout 0)

```
Logo:      (10.67, 0.41) 2.32 × 1.14 in (larger than standard)
Red rule:  (0.47, 3.16) 12.39 in wide
Placeholders:
  Title:    (0.46, 3.23) 12.41 × 0.60 in
  Subtitle: (0.46, 3.87) 12.40 × 0.60 in
  Presenter:(5.0, 5.24) 7.87 × 0.40 in
  Role:     (5.0, 5.68) 7.87 × 0.40 in
  Company:  (5.0, 6.13) 7.87 × 0.40 in
  Date:     (5.0, 6.57) 7.87 × 0.40 in
```

### Shell preservation rules

| Must preserve | Must not |
|---------------|----------|
| White-field dominance (50%+ empty) | Oversized logo heroics |
| Small top-right logo | Thick red banner shell |
| Thin red rule | Dark dashboard default |
| Quiet footer micro-band | Gradient or glass styling |
| Narrow title stack | Dropping footer for "cleanliness" |
| Shell stays calm on dense slides | Shell reinvention per topic |

### Layout inventory

| `python-pptx` index | Layout name | Use |
|--------------------:|-------------|-----|
| 0 | `1. Title slide_1` | Title slide (center band) |
| 1 | `1_2. Title slide_2` | Title slide variant with visual field |
| 2 | `2. Title slide_2` | Title slide variant with hero image |
| 3 | `4. Contents` | Table of contents / agenda |
| 4 | `5_Section slide` | Section divider |
| **5** | `6. Standard Slide` | **Primary content slide** |
| 6 | `6_Last Slide` | Closing slide only — not standard content |
| 7 | `Blank slide (no brand)` | No brand — avoid for Astemo work |
| 8 | `1_6. Standard Slide` | Standard variant with title placeholder |

## Per-Format Rules

### PPTX (PowerPoint)

1. **Template-fill only.** Start from canonical template. Never create from scratch.
2. Preserve slide masters, layouts, logos, colors, fonts, geometry.
3. Select by layout name when possible; for `python-pptx`, assert `slide_layouts[5].name == "6. Standard Slide"` before using the standard content slide.
4. Use layout `6. Standard Slide` (`python-pptx` index 5) or `1_6. Standard Slide` (`python-pptx` index 8) for content.
5. **Shell elements must be inherited from the selected layout.** Never recreate, restyle, reposition, or replace the logo, red rule, footer, page number, or CONFIDENTIAL badge on individual slides.
6. If the shell appears missing, stop and repair the template/layout selection. Do not draw a replacement shell.
7. Lock shell geometry before adding content.
8. Apply semantic colors inside content layer only, never in shell.
9. Content can get dense. Shell stays quiet.
10. Tables: brandRed `#B6001A` headers, white text, Arial; keep text readable and use alternating `#FFFFFF` / `#F2F2F2` rows.
11. Generator sequence: choose layout by name → assert index/name → inherit shell → fill title → add content → verify shell calm.

**Canonical template:** `~/Library/CloudStorage/Dropbox/Astemo_Context/09_Reference/Design_System/01_Canonical/FINAL_202504_Astemo PPT Template_simple EN.pptx`

### XLSX (Excel)

```python
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side

ASTEMO = {
    'hdr_fill':      PatternFill('solid', fgColor='B6001A'),
    'hdr_font':      Font(name='Arial', bold=True, color='FFFFFF', size=11),
    'section_fill':  PatternFill('solid', fgColor='333333'),
    'section_font':  Font(name='Arial', bold=True, color='FFFFFF', size=11),
    'data_font':     Font(name='Arial', size=10, color='232323'),
    'alt_fill':      PatternFill('solid', fgColor='F2F2F2'),
    'border':        Border(
        left=Side(style='thin', color='D9D9D9'),
        right=Side(style='thin', color='D9D9D9'),
        top=Side(style='thin', color='D9D9D9'),
        bottom=Side(style='thin', color='D9D9D9'),
    ),
    'hdr_border':    Border(bottom=Side(style='medium', color='232323')),
    'center':        Alignment(horizontal='center', vertical='center', wrap_text=True),
    'wrap':          Alignment(vertical='top', wrap_text=True),
    'complete':      PatternFill('solid', fgColor='C6EFCE'),
    'in_progress':   PatternFill('solid', fgColor='FFEB9C'),
    'open':          PatternFill('solid', fgColor='FFC7CE'),
    'not_started':   PatternFill('solid', fgColor='D9D9D9'),
    'target':        PatternFill('solid', fgColor='BDD7EE'),
    'tab_primary':   'B6001A',
    'tab_schedule':  '1F4E79',
    'tab_notes':     'ED7D31',
    'tab_dashboard': '497F37',
}
```

**Rules:**
1. Headers: `#B6001A` fill, white Arial bold 11pt. NEVER `#C00000`.
2. Alternating rows: `#FFFFFF` / `#F2F2F2`.
3. Status fills when Status column exists.
4. Freeze panes on header row + key columns.
5. Auto-filter on.
6. Landscape print orientation.

### DOCX (Word)

1. Arial body text, 11pt, `#232323`.
2. Headings: Arial bold, `#B6001A` for H1, `#232323` for H2+.
3. Tables: brandRed header row, alternating row shading.
4. Margins: 1 in all sides minimum.
5. Footer: "Astemo" identifier, page number, confidentiality when needed.

### MD / MMD (Markdown / Mermaid)

1. **Must remain readable without CSS.** Plain markdown survives first.
2. Portable grammar: headings, tables, blockquotes.
3. Explicit labels: `Status`, `Evidence`, `Risk`, `Next action`, `Owner`, `Due`.
4. Decision tables: `Decision | Evidence | Owner | Due | Status`.
5. Action tables: `Priority | Action | Owner | Due | Status`.
6. Mermaid: prefer `flowchart`, `sequenceDiagram`, `stateDiagram-v2`.
7. Avoid when portable: `xychart`, `pie`, `timeline`, HTML labels.
8. Astemo red `#B6001A` for Mermaid node accents. Not default blue.
9. No Shadow/Craft/agent branding in visible output. Automation details in frontmatter or fenced `shadow-*` blocks only.

**Typora theme:** `~/Library/CloudStorage/Dropbox/Astemo_Context/09_Reference/Design_System/` → see `themes/typora/astemo.css`

### HTML

1. Emulate the shell, not just the palette.
2. White field first.
3. Small logo or slogan mark where medium requires it.
4. Thin structural red accents (`border-bottom: 2px solid #B6001A`).
5. Quiet footer metadata.
6. No dark treatment unless a source-backed use case clearly exists.
7. CSS adapter: `~/Library/CloudStorage/Dropbox/Astemo_Context/09_Reference/Design_System/` → `themes/adapters/astemo-md.css`

### PNG / JPG / TIFF (Images)

1. Logo minimum size: 10mm / 28px. Corporate slogan minimum: 20mm / 60px.
2. Maintain logo isolation area (clear space around mark).
3. Default positive display: red. Monochrome fallback: black. Negative: white on dark.
4. No forbidden transforms: no distort, no rotation, no perspective, no outline, no shadows, no gradient fills, no splitting into multiple colors, no glow effects on dark backgrounds.
5. Do not combine logo with other letters, logos, or marks.
6. Do not use logo as pattern, decoration, or background ornament.

## Brand Truth (from official guidelines)

### What Astemo is

- **Precision brand, not decorative.** Logo is master artwork, not illustration to reinterpret.
- **Consistency = trust mechanism.** Every surface should prefer disciplined repetition over local creativity.
- **Visibility outranks expression.** If a creative move weakens recognition, it is brand damage.
- **Dual register:** Strict corporate shell + forward-looking technology narrative. Both must coexist.

### Non-negotiables

- `Astemo` with capital `A`, lowercase `stemo`. Not `ASTEMO` in sentences.
- Logo default: red. Monochrome: black. Negative: white.
- Company name: single line, same color throughout.
- 12 forbidden transforms (see Images section above).

### What "more Astemo" means

- Stronger red rule lines, not louder decoration.
- Clearer title bars and section dividers, not more effects.
- Higher contrast and cleaner spacing, not more visual noise.
- Corporate discipline in tables, metadata, labels.
- Minimal flourish. Brand confidence from control.

### What is false Astemo

- Oversized logo as main gesture
- Thick red banners everywhere
- Dark UI with red accents
- Decorative gradients
- Heroic marketing layout energy
- Dense dashboard chrome without whitespace
- Removing footer/confidential structure

## Cross-App Adapters

### Typora (Markdown editor)

Astemo Typora theme emulates the PPTX shell in CSS.

- **Installed theme:** `~/Library/Application Support/abnerworks.Typora/themes/astemo.css`
- **Source:** `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/typora/astemo.css`
- **Logo asset:** `.../themes/typora/astemo/astemo-logo.png`

**Shell emulation in CSS:**
- `#write::before` — top-right Astemo logo (132x65 px, same proportion as PPTX)
- `#write > *:first-child::before` — thin red horizon rule (`2px solid #B6001A`)
- Footer via CSS variables (`--ads-doc-footer`)

**Key CSS variables (aligned with token table):**
```css
--astemo-surface: #ffffff;
--astemo-surface-alt: #f2f2f2;
--astemo-surface-soft: #f7f7f7;
--astemo-text: #232323;
--astemo-text-secondary: #4d4d4d;
--astemo-text-muted: #737373;
--astemo-red: #b6001a;
--astemo-red-deep: #8f0014;
--astemo-rule: #d9d9d9;
--astemo-rule-strong: #b3b3b3;
--astemo-selection: rgba(182, 0, 26, 0.13);
```

**Rules:** Content must be readable without CSS (portable Markdown first). Shell (logo, rule, footer) is visual chrome, not content. No Shadow/Craft branding.

### Obsidian (knowledge base)

Obsidian is a **portable-first knowledge layer**. Custom CSS/snippets may improve rendering, but the `.md`, `.base`, and `.canvas` files must still communicate meaning without styling.

#### Obsidian note contract (`.md`)

Use properties/frontmatter to carry routing metadata and keep visible body clean:

```yaml
---
title: "Astemo Design System — Example"
tags:
  - astemo
  - design-system
program: FH4S
status: active
classification: astemo_internal
cssclasses:
  - astemo
---
```

Body structure:

- Use `[[wikilinks]]` for vault notes; Markdown links only for external URLs.
- Use explicit fields: `Status`, `Evidence`, `Risk`, `Next action`, `Owner`, `Due`.
- Use callouts with semantic meaning: `> [!info]`, `> [!warning]`, `> [!danger]`, `> [!success]`.
- Keep Shadow/Craft/agent provenance out of visible body; if needed, use a hidden `%% comment %%` or sidecar manifest.

#### Obsidian Bases (`.base`)

Astemo Bases are structured views over notes; they are not where brand chrome lives.

Canonical work-tracker columns:

```yaml
views:
  - type: table
    name: "Astemo work items"
    order:
      - file.name
      - status
      - priority
      - owner
      - due
      - evidence
      - risk
      - next_action
```

Rules:

- Use `displayName` for business-readable labels: `Next action`, `Evidence`, `Risk`.
- Use formulas for status labels/icons only when meaning is also present as text.
- Group by `status` or `program`; sort by `priority` then `due`.
- Do not encode meaning in color alone; Bases may render differently across Obsidian versions.

#### JSON Canvas (`.canvas`)

Use deterministic Astemo hex colors instead of relying only on Obsidian color presets:

| Canvas role | Node / edge color | Use |
|-------------|-------------------|-----|
| Primary / decision | `#B6001A` | Key decision, critical path |
| Evidence / neutral | `#F2F2F2` | Supporting documents, references |
| Information | `#2A579A` | Context / system map |
| Success | `#497F37` | Done / approved |
| Warning | `#C6A647` | Watch / pending |
| Attention | `#FF9B00` | Action needed |

Canvas rules:

- Node text is Markdown; start with a short heading and explicit `Status/Evidence/Risk/Next action` labels when useful.
- Groups represent workstreams or artifact families, not decorative containers.
- Edges need labels (`depends on`, `evidence for`, `blocks`, `updates`) when the relationship is not obvious.
- Keep nodes on a clean grid with 50–100 px spacing; white-field discipline still applies.
- Validate JSON: unique IDs, valid node references, no literal `\\n` mistakes.

#### Mermaid in Obsidian

Use Astemo Mermaid theme variables where renderer supports them. Prefer `flowchart`, `sequenceDiagram`, and `stateDiagram-v2`; avoid HTML labels and renderer-specific chart types for portable notes.

### Mermaid (diagrams)

**File:** `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/adapters/astemo-mermaid-theme.json`

```
theme: base
themeVariables:
  background: #FFFFFF
  primaryTextColor: #232323
  primaryBorderColor: #B6001A
  lineColor: #737373
  secondaryColor: #F2F2F2
  tertiaryColor: #F7F7F7
  fontFamily: Arial, Yu Gothic UI, sans-serif
  nodeBorder: #B6001A
  clusterBkg: #F7F7F7
  clusterBorder: #D9D9D9
  actorBkg: #FFFFFF
  actorBorder: #B6001A
  noteBkgColor: #FFF7F8
  noteBorderColor: #B6001A
```

**Preferred:** `flowchart`, `sequenceDiagram`, `stateDiagram-v2`. **Avoid:** `xychart`, `pie`, `timeline`, HTML labels.

### Pandoc (MD to HTML/PDF)

**File:** `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/adapters/astemo-pandoc.yaml`

```
from: markdown+yaml_metadata_block+pipe_tables+fenced_code_blocks
standalone: true
toc: true
css: astemo-md.css
variables:
  mainfont: Arial
  monofont: Menlo
  geometry: margin=0.55in
  linkcolor: "2A579A"
```

### HTML (web artifacts)

**CSS adapter:** `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/adapters/astemo-md.css`

- White field first. No dark treatment.
- Small logo or slogan mark.
- Thin structural red accents (`border-bottom: 2px solid #B6001A`).
- Quiet footer metadata.
- CSS variables mirror Typora theme.

### Craft (agent chat)

Craft uses its own rendering. Agent output is Markdown.
- Use Astemo Markdown grammar for all Craft responses.
- Use `datatable` / `spreadsheet` blocks with Astemo header colors.
- Use `html-preview` for Astemo-branded HTML artifacts.
- Use `image-preview` for PNG/JPG exports.
- No Shadow/Craft branding in visible output.

### File manifests and artifact indexes

Use portable Astemo file-type/work icons for generated indexes, relay packets, and review manifests.

| Surface | Source |
|---------|--------|
| File-type icons | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/icons/filetypes/` |
| File-type manifest | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/icons/filetypes/filetype-icons.json` |
| Work/status icons | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/icons/work/` |
| Work icon manifest | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/icons/work/work-icons.json` |

Rules:

- Cover common Astemo artifacts: `.pptx`, `.xlsx`, `.docx`, `.pdf`, `.md`, `.mmd`, `.html`, `.png`, `.jpg`, `.jpeg`, `.tif`, `.tiff`, `.csv`, `.json`, `.base`, `.canvas`.
- Use icons as navigation aids, not decorative brand replacements.
- Do not use emojis as formal file-type icons in review packets when SVG icons are available.
- Generated indexes must remain usable without icons: include file names, formats, review state, and owner/status text.

## References

| Source | Path |
|--------|------|
| Design system index | `~/Library/CloudStorage/Dropbox/Astemo_Context/09_Reference/Design_System/INDEX.md` |
| Canonical PPTX template | `...Design_System/01_Canonical/FINAL_202504_Astemo PPT Template_simple EN.pptx` |
| PPT style guide V10 | `...Design_System/01_Canonical/-En-_Astemo-ppt-style-guide_V10.pptx` |
| Slide master reverse-engineering | `...Design_System/05_Extracted/2026-05-05-astemo-ppt-template-slidemaster-reverse-engineering.md` |
| Tokens JSON | `...Design_System/05_Extracted/2026-04-28-astemo-ppt-design-system-tokens.json` |
| Generator contract | `...Design_System/05_Extracted/2026-04-29-astemo-generator-contract.json` |
| Shell anatomy | `...Design_System/05_Extracted/2026-04-28-astemo-shell-anatomy.md` |
| True design truth | `...Design_System/05_Extracted/2026-04-28-astemo-true-design-reverse-engineering.md` |
| Public brand truths | `...Design_System/05_Extracted/2026-04-29-astemo-public-brand-truths.md` |
| Shell spec | `...Design_System/05_Extracted/2026-04-29-astemo-ppt-shell-spec.md` |
| Guidelines V4.1 | `...Design_System/06_Guidelines/260312_Astemo_Guidelines_Ver4_1_EN - Crystallization.md` |
| Display manual | `...Design_System/06_Guidelines/20260316_Astemo_Display_Manual_for_Applications_en - Crystallization.md` |
| Portable tokens | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/astemo-md-mmd-tokens.json` |
| Design contract | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/astemo-md-mmd-design-system.md` |
| Typora CSS | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/typora/astemo.css` |
| Typora logo | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/typora/astemo/astemo-logo.png` |
| HTML/Pandoc CSS | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/adapters/astemo-md.css` |
| Mermaid theme | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/adapters/astemo-mermaid-theme.json` |
| Pandoc defaults | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/adapters/astemo-pandoc.yaml` |
| Reference decks | `...Design_System/10_Reference_Decks/` (5 real working decks) |
| Deck anatomy | `...Design_System/11_Deck_Anatomy/` (5 slide-by-slide breakdowns) |
| Design system JSON | `...Design_System/DESIGN_SYSTEM.json` |
