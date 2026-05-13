---
name: Astemo Excel Online
description: 'Use when the user needs: Headless Excel Online automation for Astemo — read trackers, schedules, quality forms in browser. Enriches with FH4S column mapping. Trigger on "excel online", "open tracker online", "spreadsheet web", "excel in sharepoint", "online workbook", "Astemo tracker", "FH4S tracker online".'
icon: icon.svg
metadata:
  depends_on: [astemo-pipeline,astemo-work-context]
  replaces: [hl-excel-online-astemo]
  filePattern: []
  bashPattern:
  - excel online
  - spreadsheet online
  - tracker online
  - workbook web
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-excel-online
  invisible_first: true
  tier: 3
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# astemo-excel-online — Headless Excel Online (Astemo)

Astemo-enriched version of hl-excel-online. Extends general Excel Online automation with:
- Change Point Tracker column mapping (47 columns, 5 sheets)
- SASG form structure awareness (DFMEA, Quality Schedule)
- Astemo branding token detection (headers, conditional fills)
- DE Activity Schedule structure

## Account & Profile
- **Email:** sabarish.duvvuru.qx@astemo.com
- **Browser:** Chrome "Sabarish" profile (always)

## Key Workbooks

| Workbook | Location | Sheets |
|----------|----------|--------|
| FH4S Change Point Tracker | SharePoint/FH4S | Summary, Change Points, Carry Over, DWG Plan, Notes |
| DE Activity Schedule | Smartsheet (ID: 2231837406482308) | Gantt view |
| DFMEA (K11-03A) | SharePoint/FH4S/Quality | DFMEA worksheet |
| Quality Schedule (X10-02A) | SharePoint/FH4S/Quality | FormB |

## Change Point Tracker Column Map

When reading the Change Point Tracker online, key columns:
| Col | Header | Content |
|-----|--------|---------|
| A | No. | Row number |
| B | Part Number | 4E0A-xxxx-xxx |
| C | Part Name (EN) | English name |
| D | Part Name (JP) | Japanese name (preserve exactly) |
| E | Category | Change/Carry-over |
| F-H | Change Description | EN/JP/Details |
| I-K | Risk | Severity/Occurrence/Detection |
| L | RPN | Risk Priority Number |
| M-N | Status | Current/Target |
| O-Q | DFMEA | Reference/Status/Date |
| R-T | DRBFM | Reference/Status/Date |
| U-W | Control Plan | Reference/Status/Date |

## Reading Strategy for Astemo Workbooks

1. **Snapshot first** — identify which workbook type
2. **Check sheet tabs** — navigate to relevant sheet
3. **Navigate to Name Box** — jump to data ranges
4. **Japanese text** — preserve exactly, never translate
5. **Status colors** — Green=#C6EFCE (done), Yellow=#FFEB9C (in-progress), Red=#FFC7CE (blocked)

## Workflow

1. Open workbook via SharePoint or direct URL
2. Identify workbook type from title/headers
3. Apply appropriate column mapping
4. Extract relevant data
5. If tracker: sync with local `change-points.json`
6. If quality form: validate against SASG structure

## Prefer Local Over Online

For the Change Point Tracker, prefer the local copy at:
`fh4s/outputs/workbooks/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx`

Only use Excel Online when:
- Checking if JP has updated the online version
- Comparing online vs local for drift
- Reading workbooks not yet downloaded

## Uses hl-excel-online
All Playwright operations follow hl-excel-online patterns. This skill adds Astemo context layer on top.

## Pipeline

```
Navigate to Workbook (SharePoint URL / direct link)
  ↓
Identify Workbook Type (Change Point Tracker / SASG form / Schedule)
  ↓
Apply Column Mapping (47-col tracker / K11-03A DFMEA / X10-02A Quality Schedule)
  ↓
Extract Data (cell ranges, status columns, Japanese text preserved verbatim)
  ↓
Validate & Sync
  ├── Local copy sync → fh4s/outputs/workbooks/
  ├── SASG validation → ShadowArchive/80-reports/
  └── Training pairs → astemo-pipeline
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `summary` / default | "open tracker", "check tracker online" | Active/open items summary — status counts, recent changes |
| `full` | "all rows", "full tracker" | Complete row extract with all 47 columns |
| `diff` | "compare online vs local", "what changed" | Side-by-side diff of online vs local copy |
| `jp-updates` | "did JP update", "check for updates" | Scan for recent edits by JP counterparts |
| `validate` | "validate form", "check SASG" | Structure validation against SASG template |

Default output is compact: status summary + count of open/in-progress/closed items.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Tracker local copy | `fh4s/outputs/workbooks/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx` | Prefer local over online |
| SASG validation report | `ShadowArchive/80-reports/sasg-validate-YYYY-MM-DD-<form>.md` | Structure compliance check |
| Tracker diff report | `ShadowArchive/80-reports/tracker-diff-YYYY-MM-DD.md` | Online vs local comparison |
| Training pairs | → `astemo-pipeline` | Column classification signals |

Do not leave extracted data in `/tmp` — always route to a durable path.

## Fallback Chain

```
1. Excel Online (Playwright)  (primary — live data, authenticated)
   ↓ (workbook locked / JS timeout / rendering failure)
2. Local xlsx copy  (fh4s/outputs/workbooks/ — may be stale, disclose age)
   ↓ (no local copy / critically outdated)
3. SharePoint REST API download  (if hl-sharepoint available)
   ↓ (API unavailable / VPN down)
4. Ask operator  (explicit disclosure: "Workbook unavailable online — please download manually")
```

Always disclose which source was used and how old the data is.

## Prerequisites

- **Chrome "Sabarish" profile** — authenticated to M365
- **hl-excel-online patterns** loaded — Playwright cell/range extraction methods
- **astemo-work-context** loaded — for program-specific column mapping
- **Local tracker copy** exists — at `fh4s/outputs/workbooks/` for diff operations
- **VPN** — required if off corporate network

## Error Handling

| Failure | Recovery |
|---------|----------|
| Workbook locked by another user | Disclose who has it open; fall back to local copy |
| Co-author conflict | Re-snapshot after 5s; if persistent, use local copy |
| JS rendering timeout | Wait 5s, retry snapshot; Excel Online is JS-heavy |
| SharePoint sync delay | Disclose potential staleness; suggest manual refresh in browser |
| Japanese text corrupted in extraction | Re-extract with UTF-8 encoding; never translate corrupted text |
| Sheet tab not found | List available tabs; ask operator which to use |

## Contract

- **Preserve Japanese text exactly** — never translate, transliterate, or approximate JP column values.
- **Do not modify online tracker directly** — always extract to local copy, then upload if operator approves.
- **Do not expose internal tracker URLs or SharePoint paths** in customer-facing artifacts.
- **Do not store tracker cell content in training pairs** — classification metadata only (status, program, risk level).
- SASG form validation is **internal quality assurance** — do not surface in Honda/customer deliverables.
- Diff reports are operator-only — do not relay to Dropbox without explicit approval.
