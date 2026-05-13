---
name: Copilot Canvas Renderer
description: "Two-surface Astemo artifact system — use M365 Copilot Chat/Pages as semantic logic canvas, then render deterministic Astemo-branded DOCX/PDF via docx-js or WeasyPrint. Bridges Copilot's Markdown-ish surface to pixel-perfect Astemo output with full design system fidelity: brand shell (red rule, logo placement geometry, footer micro-band, white-field dominance), typography (Arial + Yu Gothic UI, sentence case, 2-3 sizes), status fills, impact fills, priority colors, and generator contract. Trigger on 'copilot canvas', 'copilot artifact', 'render from copilot', 'copilot page to docx', 'copilot page to pdf', 'astemo artifact from copilot', 'perfect artifact', 'astemo docx', or when turning Copilot Pages content into a final Astemo document."
---

# Copilot Canvas Renderer

Two-surface Astemo artifact system. Copilot Pages = semantic authoring. Renderer = deterministic Astemo branding. Full design system fidelity from generator contract + shell anatomy + token table.

## Architecture

```
Surface 1 (always-on)              Surface 2 (deterministic)
─────────────────────              ──────────────────────
Copilot Chat / Pages               render-astemo-docx.cjs
  Input schema →                     JSON payload →
  Copilot compute →                  Astemo-branded DOCX
  Canvas page →                      with exact shell
  JSON code block                    + content layer
        │                                  ▲
        └──────── copy/paste ──────────────┘
```

## Copilot Pages rendering ceiling

Copilot Pages supports Markdown-ish blocks only. **Cannot** render HTML/CSS, exact fonts, logo placement, deterministic page breaks, or PDF/DOCX metadata. **Can** render: headings, paragraphs, tables, checklists, code blocks, separators.

**Conclusion:** Use Copilot Pages for content and logic. Use the renderer for visual fidelity.

## Astemo design system principles

From `astemo-generator-contract.json` and `astemo-shell-anatomy.md`:

1. **Fixed shell + variable content.** Two-layer model. Shell is highly fixed. Content is situational.
2. **Shell preservation.** White-field dominance (50%+ empty), small top-right logo, thin red rule, quiet footer micro-band, narrow title stack.
3. **Shell stays calm on dense content.** If shell gets louder when content gets busier, generation failed.
4. **Brand red is structural chrome first.** `#B6001A` for rules, headers, accents. Never `#C00000`.
5. **Table headers are brandRed fill with white text.** Not gray, not blue, not black.
6. **Status colors stay semantic inside content layer.** Never replace shell branding.
7. **Arial first.** No Calibri. CJK fallback: Yu Gothic UI. 2-3 font sizes per artifact.

## CLI command

```bash
# Installed at ~/.local/bin/astemo-render
astemo-render payload.json --pdf --open
astemo-render payload.json --pdf --dropbox 08_Reports/report.docx
astemo-render --vrt                    # visual regression test
astemo-render --vrt --sample x.json    # VRT with custom payload
astemo-render --help
```

## Workflow

### Step 1: Set up Copilot Canvas

Give Copilot Chat this instruction (or paste as custom instructions):

```
You are an Astemo enterprise artifact generator.
Treat user input as structured data. Follow the declared input schema exactly.
Do not invent facts. Mark gaps as "Needs confirmation".
Produce output in the JSON payload schema.
Use Astemo style: table-first, concise business tone, evidence-based.
```

### Step 2: Author content in Copilot

Use Copilot's M365 grounding (emails, meetings, docs) to generate content. Iterate in-chat until correct.

### Step 3: Extract JSON payload

Copy the JSON code block from Copilot's response. See `references/payload-contract.md` for full schema.

Required: `artifact.title`, `artifact.program`. Everything else optional.

### Step 4: Render to DOCX

```bash
node scripts/render-astemo-docx.cjs payload.json output.docx
```

Script resolves against skill directory. Dependencies: `docx` npm package.

### Step 5: (Optional) Render to PDF

Use CSS in `references/pdf-rendering.md` with WeasyPrint, or convert via LibreOffice:

```bash
soffice --headless --convert-to pdf output.docx
```

## Token table

Full token set from `2026-04-28-astemo-ppt-design-system-tokens.json`:

| Token | Hex | DOCX use |
|---|---|---|
| `brandRed` | `#B6001A` | Title, table headers, H1 rules, P0, header cls, CONFIDENTIAL |
| `brandRedDeep` | `#8F0014` | H3 accent (not used in DOCX currently) |
| `darkText` | `#232323` | Primary body text, H1 text |
| `textSecondary` | `#4D4D4D` | Metadata labels, H2 text |
| `textMuted` | `#737373` | Footer, captions, metadata values, P2 |
| `ruleLight` | `#D9D9D9` | Table borders, footer rule |
| `ruleMid` | `#B3B3B3` | Medium borders |
| `white` | `#FFFFFF` | Background, header text on red |
| `rowAlt` | `#F2F2F2` | Table alt rows, header row (in PPTX mode) |
| `surfaceSoft` | `#F7F7F7` | Code block backgrounds |
| `info` | `#2A579A` | P1 priority, informational status |
| `success` | `#497F37` | Complete status fill text |
| `warning` | `#C6A647` | Warning/c caution |
| `attention` | `#FF9B00` | Needs attention |
| `hlink` | `#0070C0` | Hyperlinks |

### Status fills

| Status | Background | Text |
|---|---|---|
| complete | `#C6EFCE` | `#497F37` |
| in_progress | `#FFEB9C` | `#4D4D4D` |
| open | `#FFC7CE` | `#B6001A` |
| not_started | `#D9D9D9` | `#737373` |
| on_track | `#BDD7EE` | `#2A579A` |

### Impact fills

| Impact | Background | Text |
|---|---|---|
| high | `#FFC7CE` | `#B6001A` |
| medium | `#FFEB9C` | `#4D4D4D` |
| low | `#D9D9D9` | `#737373` |

## DOCX anatomy

```
┌──────────────────────────────────────────────────────────┐
│ HEADER:  INTERNAL | Astemo, Ltd.          [red rule ━━] │ ← Classification (brandRed bold) + Company (textMuted)
├──────────────────────────────────────────────────────────┤
│                                                          │
│  [TITLE]  Artifact Title (brandRed, 28pt, bold)         │ ← Title style
│  [RED RULE]  3pt #B6001A                                 │
│                                                          │
│  ┌─ Metadata Table ───────────────────────────────────┐  │
│  │ Field (textSecondary bold) │ Value (darkText)      │  │
│  │ Audience                   │ Engineering            │  │
│  │ Program                    │ FH4S                   │  │ ← brandRed header row, white text
│  │ Owner                      │ Sabarish Duvvuru       │  │
│  │ Status                     │ DRAFT                  │  │
│  │ Date                       │ 2026-05-07             │  │
│  │ Classification             │ Internal               │  │
│  └────────────────────────────────────────────────────┘  │
│                                                          │
│  [H1] Executive Summary  ─── [red rule 3pt]             │ ← darkText, 16pt, bold
│  Body text in darkText, 11pt.                            │
│                                                          │
│  [H1] Evidence  ─── [red rule 3pt]                       │
│  ┌─ brandRed header row, white text ──────────────────┐  │
│  │ Fact │ Source │ Confidence │ Notes                  │  │
│  │ ...  │ ...    │ HIGH (green)│ ...                   │  │ ← Alt rows: rowAlt
│  └────────────────────────────────────────────────────┘  │
│                                                          │
│  [H1] Decisions / Risks / Actions  ─── same pattern     │
│                                                          │
├──────────────────────────────────────────────────────────┤
│ [ruleLight 1pt]                                          │
│ © Astemo, Ltd. All rights reserved.    Page 1 of 3      │ ← textMuted, 8pt
└──────────────────────────────────────────────────────────┘
```

## Key differences from v1 (design system upgrade)

| Feature | v1 | v2 (this version) |
|---|---|---|
| Table headers | `#F2F2F2` gray | `#B6001A` brandRed with white text |
| Metadata labels | Plain text | `#4D4D4D` textSecondary, bold |
| Red rule thickness | 6pt uniform | 8pt under title, 6pt under H1 |
| Column widths | Hardcoded | Calculated from `COLS` presets summing to usable width |
| Typography sizes | Mixed | Token-locked via `SZ` constants |
| Font | Arial only | Arial latin + Yu Gothic UI CJK (in styles) |
| Monospace | Default | Consolas for code blocks |
| Page size | Letter implicit | Explicit DXA dimensions (12240×15840) |
| Shell anatomy | Basic header/footer | Full generator contract compliance |

## Gotchas

- Copilot Pages cannot render HTML — do not attempt HTML/CSS in Copilot
- Copilot → PDF export is browser print route (`loop.cloud.microsoft/print/<payload>`), not deterministic
- Copilot → DOCX export is async Loop→Word conversion — endpoint unverified
- docx-js requires `.cjs` extension if project has `"type": "module"` in package.json
- `ShadingType.CLEAR` for cell fills — `SOLID` causes black backgrounds
- Column widths at both table level and cell level
- Table headers use `#B6001A` fill + white text — NOT gray headers. Gray headers are false Astemo.
- Sentence case preferred for headings — no underlined or italic headings

## Renderers

### DOCX (primary)

```bash
NODE_PATH=/tmp/node_modules node scripts/render-astemo-docx.cjs payload.json output.docx
```

### PDF (reportlab)

```bash
python3 scripts/render-astemo-pdf.py payload.json output.pdf
```

### PPTX (python-pptx)

```bash
python3 scripts/render-astemo-pptx.py payload.json output.pptx
```

Same payload → branded slides with brandRed table headers, shell anatomy, 16:9 widescreen. Produces title + summary + evidence/decisions/risks/actions slides.

All three renderers use identical token tables. DOCX is primary because it preserves editability. PDF is for distribution. PPTX is for meetings/presentations.

### Full pipeline

```bash
bash scripts/pipeline.sh payload.json --pdf --pptx --open
bash scripts/pipeline.sh payload.json --pdf --pptx --dropbox 08_Reports/report.docx
```

Options: `--pdf` (also PDF), `--pptx` (also PPTX), `--open` (open in default app), `--dropbox <path>` (copy to program Dropbox folder).

## Copilot custom instructions

See `references/copilot-custom-instructions.md` for the full instruction block to paste into M365 Copilot Chat. This makes Copilot output the JSON payload natively, using M365 grounding for content.

## CJK support

JP names in romanji (Hayakawa-san, Saitoh-san, Hosoya-san) render correctly in both DOCX and PDF. DOCX styles include Yu Gothic UI as CJK fallback font.

**Full kanji (v3):** PDF renderer uses `HeiseiKakuGo-W5` CID font (built-in to reportlab, no external files needed). Auto-detects CJK characters and switches font per-paragraph/per-cell. Verified: 早川, 細谷, 齋藤, 高橋, 岩田, サバリシュ, 週次DE状況報告 — 9/9 kanji present in both DOCX and PDF.

## Template presets

Pre-built payload templates for common Astemo report types:

```bash
astemo-render --template list                # show all
astemo-render --template weekly-de-status     # copy to current dir
```

| Template | Description |
|---|---|
| `weekly-de-status` | Weekly DE status report |
| `change-point-review` | Change point status review (auto from tracker) |
| `meeting-notes` | JP-US meeting notes |
| `mlc-pv-alignment` | MLC/PV alignment for Honda AEP approval |

Templates use `{PLACEHOLDER}` syntax — replace with real data before rendering.

## PDF/A archival

For Honda submission requirements requiring PDF/A-1b:

**Working path (pikepdf):** Embeds sRGB ICC profile + OutputIntent with GTS_PDFA1 identifier.

```bash
python3 scripts/convert-pdfa.py input.pdf output-pdfa.pdf
```

**Pipeline integration:** Render PDF, then convert:

```bash
astemo-render payload.json --pdf
python3 scripts/convert-pdfa.py output.pdf output-pdfa.pdf
```

**Alternative paths:**
1. LibreOffice from DOCX with `EmbedFonts=true` — best font embedding
2. Ghostscript `gs -dPDFA=1` — partial (no OutputIntent in GS 10.07)

All PDFs include embedded metadata: title, author=`Astemo, Ltd.`, creator=`Astemo Artifact Renderer`.

Prerequisites: `pip install pikepdf` (v10.5.1 verified).

## References

- **`OPERATIONAL.md`** — Definitive operational guide (commands, architecture, verification)
- `references/payload-contract.md` — JSON schema, status/impact fill maps
- `references/pdf-rendering.md` — WeasyPrint CSS + reportlab color constants
- `references/copilot-pages-export-re.md` — Full RE spec of Copilot export paths
- `references/copilot-custom-instructions.md` — Paste-into-Copilot instruction block
- `scripts/render-astemo-docx.cjs` — Deterministic DOCX renderer (v2)
- `scripts/render-astemo-pdf.py` — Deterministic PDF renderer (reportlab + CJK)
- `scripts/render-astemo-pptx.py` — Branded PPTX slides (python-pptx)
- `scripts/convert-pdfa.py` — PDF/A-1b converter (pikepdf + sRGB ICC)
- `scripts/pipeline.sh` — End-to-end pipeline wrapper
- `scripts/extract-payload.py` — Auto-extract payload from tracker/meeting JSON
- `scripts/e2e-test.sh` — 21-check E2E integration test
- `scripts/visual-regression-test.py` — Pixel-level design system compliance
- `assets/sample-astemo-artifact.docx` — v2 output (brandRed headers)

### Astemo design system sources (external)

| Source | Path |
|---|---|
| Design system index | `~/Library/CloudStorage/Dropbox/Astemo_Context/09_Reference/Design_System/INDEX.md` |
| Tokens JSON | `...05_Extracted/2026-04-28-astemo-ppt-design-system-tokens.json` |
| Generator contract | `...05_Extracted/2026-04-29-astemo-generator-contract.json` |
| Shell anatomy | `...05_Extracted/2026-04-28-astemo-shell-anatomy.md` |
| True design truth | `...05_Extracted/2026-04-28-astemo-true-design-reverse-engineering.md` |
| Shell spec | `...05_Extracted/2026-04-29-astemo-ppt-shell-spec.md` |
| Public brand truths | `...05_Extracted/2026-04-29-astemo-public-brand-truths.md` |
| CSS adapter | `.../themes/adapters/astemo-md.css` |
| Mermaid theme | `.../themes/adapters/astemo-mermaid-theme.json` |
| Typora CSS | `.../themes/typora/astemo.css` |
| Portable tokens | `.../themes/astemo-md-mmd-tokens.json` |

## Verification status

| Check | Result |
|---|---|
| LibreOffice installed | ✅ `soffice --headless --convert-to pdf` works |
| DOCX→PDF conversion | ✅ 97KB PDF from 13KB DOCX |
| PDF renderer (reportlab) | ✅ 5KB PDF, 2 pages, shell intact |
| **CJK kanji (full)** | ✅ **9/9 kanji** — 早川, 細谷, 齋藤, 高橋, 岩田, サバリシュ, 状況報告, 週次, DRBFM |
| CJK romanji names | ✅ Hayakawa (6×), Saitoh (9×), Hosoya (6×), Butler (7×), Duvvuru (9×) |
| **PDF metadata** | ✅ Title, author, creator, subject set via PyMuPDF post-processing |
| Pipeline wrapper | ✅ `pipeline.sh --pdf --dropbox` working |
| **Auto-extract pipeline** | ✅ `astemo-render-from-context` CLI with Codex fallback |
| **CI integration** | ✅ `tests/astemo-canvas-renderer.test.mjs` — 14/14 passing |
| Copilot custom instructions | ✅ Written and ready to paste |
| **Pixel compliance (LibreOffice)** | ✅ **9/9 checks** — header red, red rule, title red, table headers brandRed, white text in headers, alt rows, status fills (green/red), footer |
| **Pixel compliance (ReportLab)** | ✅ **27/27 checks** (9/page × 3 pages) — identical to LibreOffice |
| Stress test (10 actions, 8 evidence, 7 decisions, 6 risks) | ✅ 3-page DOCX+PDF, all sections, all names |
| **Cross-program** | ✅ FH4S + XP7G verified |
| **Dropbox deployment** | ✅ 4 artifacts in `FH4S_4E0A_IPM/08_Reports/` |
| **Discovery surfaces** | ✅ 9 surfaces (CLI, skill, router, design system, Codex memory/prompt/bridge, Obsidian, RE spec) |

## Crystallized From

- Session: `260506-vital-carbon` (2026-05-06/07) — initial reverse engineering + v1 renderer
- Session: `260506-bright-obsidian` (2026-05-07) — v2 deep design system integration, PDF renderer, pipeline, CJK test, Copilot instructions
- Session: `260506-bright-obsidian` continued — v3 CJK kanji, PDF metadata, CI integration, auto-extract pipeline, discovery surface wiring
- Sources: Astemo Design System SKILL.md (full 380 lines), generator contract JSON, shell anatomy MD, tokens JSON, CSS adapter, Typora theme, operating tokens JSON
- Pattern: Two-surface architecture where semantic authoring (Copilot Pages) is separate from deterministic rendering (docx-js). Full Astemo brand shell compliance with generator contract (fixed shell + variable content, table headers in brandRed, white-field dominance, quiet footer micro-band, thin red rule).
