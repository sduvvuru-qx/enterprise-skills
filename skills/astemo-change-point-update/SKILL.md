---
name: Astemo Change Point Update
description: "Update the Change Point Tracker with new information from meetings, emails, or transcripts. Parse input, identify which rows to update, apply changes, log in Change Log sheet, re-theme with Astemo branding, save, sync to cloud list. Cross-program: FH4S, FH4L, XP7G, XY9H, P703. Program resolved via astemo-work-context. Trigger on: update tracker, update change points, add to tracker, meeting notes to tracker, log this in the tracker, tracker update, change point update."
icon: icon.svg
metadata:
  depends_on: [astemo-work-context,astemo-xlsx]
  replaces: [fh4s-change-point-update]
  priority: 90
  filePattern: '**/*Tracker*.xlsx'
  bashPattern: tracker|change.point
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-change-point-update
  icon_style: craft-category-glyph-v1
---

# Astemo Change Point Update

Cross-program change point tracker CRUD. Program resolved at runtime.

## Program Resolution

```
program = astemo-work-context.resolve_program()
paths:
  tracker:  {dropbox_root}/{tracker_path}
  cloud_list: FH4S-Change-Points on USGRPxEV (or per-program list)
```

## Tracker Structure (standard 47-column Astemo format)

Key columns (shared across programs):
| Col | Header | Purpose |
|---|---|---|
| A | Item # | Sequential |
| B | L3 Part Number | Astemo part number |
| C | Part Name | English name |
| D | Part Name (JP) | Japanese name |
| E | CP (Yes/No) | Change Point flag |
| F | CP Description | What changed |
| G | Risk Level | H/M/L |
| H | Status | Not Started/Open/In Progress/Finalized/Complete |
| I-K | B1/B2/B3 | Buyer/SQ/DE contacts |
| L | Lead Region | JP or US |
| M | KA Supplier | Current Japan supplier |
| N | IAP Supplier | Target US supplier |
| O | Supplier Change | Yes/No |
| P-R | Drawing info | Number, Rev, Date |
| S | 2WD/AWD | Applicability |
| W-Z | Notes columns | US DE, JP DE, SQ, BUP |

Sheets: Dashboard, Change Log, Changepoints (main), Meeting Notes, DE Activity

## Workflow

### 1. Parse Input
Accept: meeting transcript, email content, direct instructions, bullet points.

Extract:
- Which part(s) affected (part number, name, or item #)
- Which column(s) to update
- New value(s)
- Source (meeting date, email from, decision by whom)

### 2. Match to Tracker Rows
Find row by part number (col B), part name (col C), or item # (col A).
If ambiguous → list candidates, ask for confirmation.

### 3. Apply Updates
For each update:
1. Record old value
2. Write new value
3. Add to Change Log: `[datetime, who, column, old_val, new_val, source]`
4. Apply status/risk conditional fills from `astemo-xlsx`

### 4. Sync to Cloud List
If browser authenticated on `astemogroup.sharepoint.com`:
1. Cache digest via `evaluate` contextinfo
2. Find list item by `ItemNumber` using `$filter`
3. MERGE changed fields via POST

If sync fails → continue with XLSX-only, log failure, retry later.

### 5. Re-Theme
`astemo-xlsx` constants: #B6001A headers, white Arial bold, alternating rows white/#F2F2F2, status/risk fills, thin #D9D9D9 borders.

### 6. Save & Upload
- Backup: timestamped copy
- Save updated XLSX
- Upload to cloud (rclone/GDrive if configured)

### 7. Report
```
Updated {Program} Change Point Tracker:
- Row 12 (Part#): Status → In Progress, Notes → "..."
- Row 23 (Part#): Supplier Change → Yes
- Change Log: N entries added
- Cloud sync: ✅ / ❌ (logged for retry)
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `update` / default | "update tracker", "add to tracker" | Full update + cloud sync |
| `log` | "log this", "record change" | Change Log entry only |
| `bulk` | "mark all X as Y" | Bulk status/risk update |
| `status` | "status of tracker" | Recent changes summary |
| `sync` | "sync to cloud" | Cloud list push only |
| `report` | "generate report", "export report", "render report" | Branded DOCX+PDF via `astemo-render` |
| `dry-run` | "what would change" | Preview without writing |

## Pipeline

```
Parse Input → Match Rows → Apply Updates → Cloud Sync → Re-Theme → Save → Report
```

## Report Export

When triggered with "generate report" or "export report":

1. Extract tracker data via `openpyxl` → JSON (all rows or filtered subset)
2. Run `astemo-render-from-context` to auto-generate payload
3. Render DOCX + PDF via `astemo-render`
4. Deploy to Dropbox: `{dropbox_root}/08_Reports/{date}-{program}-ChangePoint-Review.{docx,pdf}`
5. Optionally open for review

```bash
# Direct path (after tracker extract)
astemo-render-from-context tracker-extract.json --dropbox "08_Reports/report.docx"

# Template-based path
astemo-render --template change-point-review cp-review.json
# Edit cp-review.json, then:
astemo-render cp-review.json --pdf --dropbox "08_Reports/report.docx"
```

## Fallback Chain

```
1. Local XLSX + Cloud list sync (primary)
   ↓ (cloud unavailable)
2. Local XLSX only (log sync failure)
   ↓ (XLSX locked)
3. Backup + manual edit
   ↓ (no backup)
4. Ask operator
```

## Error Handling

| Failure | Recovery |
|---|---|
| Part not found | List closest matches; ask operator |
| Ambiguous match | List candidates; ask which row |
| XLSX locked | Wait 5s, retry |
| Cloud sync fails | XLSX-only; log failure |
| JP text corrupted | Re-read UTF-8; preserve original |

## Contract

- **Cross-program** — resolve paths via `astemo-work-context`, never hardcode.
- **Always** use `astemo-xlsx` styling.
- **Always** backup before updating.
- **Always** log changes in Change Log sheet.
- **Preserve** Japanese text exactly.
- **Do not** expose tracker internals in customer-facing artifacts.
- **Do not** auto-commit to customer gates — flag `needs evidence`.
- Cloud sync is **best-effort** — XLSX is source of truth.
