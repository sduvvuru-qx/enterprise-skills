---
name: Astemo OneDrive
description: 'Use when the user needs: Headless OneDrive automation for Astemo — browse engineering files, download drawings, search project docs. Enriches with FH4S/program context. Trigger on "onedrive", "my files", "cloud files", "find drawing on onedrive", "shared with me", "astemo files".'
icon: icon.svg
metadata:
  depends_on: [astemo-presentation-prep-relay,fh4s-change-point-update,fh4s-ingest]
  filePattern: []
  bashPattern:
  - onedrive
  - my files
  - astemo files
  - cloud files
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-onedrive
  icon_style: craft-category-glyph-v1
---

# astemo-onedrive — Headless OneDrive (Astemo)

Astemo-enriched version of hl-onedrive. Extends general OneDrive automation with:
- Engineering file classification (drawings, SASG forms, trackers)
- Program-aware folder navigation (FH4S, FH4L, VEC)
- Drawing file detection → fh4s-ingest pipeline
- SASG standard file identification

## Account & Profile
- **Email:** sabarish.duvvuru.qx@astemo.com
- **Browser:** Chrome "Sabarish" profile (always)
- **URL:** https://astemo-my.sharepoint.com/personal/sabarish_duvvuru_qx_astemo_com/_layouts/15/onedrive.aspx

## Known Folder Structure

| Path | Content | Program |
|------|---------|---------|
| `/FH4S/` | IPM localization files | FH4S |
| `/FH4S/Drawings/` | PDF drawings from JP | FH4S |
| `/FH4S/Quality/` | DFMEA, DRBFM, Control Plans | FH4S |
| `/Standards/` | SASG standard documents | Cross-program |
| `/Shared/` | Cross-team shared files | All |

## File Type Classification

| Extension | Type | Action |
|-----------|------|--------|
| `.pdf` | Drawing or standard | Check for 4E0A prefix → FH4S drawing |
| `.xlsx` | Tracker or form | Check for Change Point / DFMEA / schedule |
| `.pptx` | Presentation | Honda response or DR review |
| `.docx` | Report or memo | Meeting notes or design report |

## Download → Ingest Flow

When downloading files from OneDrive:
1. Download to browser default folder
2. If engineering drawing (PDF with 4E0A): trigger `fh4s-ingest` for classification
3. If SASG standard: route to `standards/` directory
4. If tracker update: flag for `fh4s-change-point-update`

## Uses hl-onedrive
All Playwright operations follow hl-onedrive patterns. This skill adds Astemo context layer on top.

## Pipeline

```
Navigate OneDrive (astemo-my.sharepoint.com/personal/...)
  ↓
Browse / Search (folder tree or keyword query)
  ↓
Classify File Type (drawing / tracker / SASG form / presentation / report)
  ↓
Download to Local (browser default folder)
  ↓
Route to Ingest Pipeline
  ├── Engineering drawings (4E0A PDFs) → fh4s-ingest
  ├── Trackers (.xlsx) → fh4s-change-point-update
  ├── SASG standards → standards/ directory
  └── Presentations (.pptx) → astemo-presentation-prep-relay
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `list` / default | "onedrive", "my files" | Folder listing with file type icons and program tags |
| `search:X` | "find drawing on onedrive", "search X" | Keyword search across files |
| `recent` | "recently modified", "recent files" | Recently modified files sorted by date |
| `drawings` | "drawings", "PDF drawings" | Filtered view of engineering drawings only (PDF with 4E0A prefix) |
| `shared` | "shared with me" | Files shared by others |
| `download:X` | "download X" | Download specific file and route to ingest pipeline |

Default output is compact: folder listing with file type tags and program classification.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Engineering drawings | → `fh4s-ingest` | 4E0A-prefix PDFs auto-classified |
| Trackers | → `fh4s-change-point-update` | .xlsx workbooks flagged for update |
| SASG standards | → `standards/` local directory | Quality system reference docs |
| Presentations | → `astemo-presentation-prep-relay` | Customer/internal decks |
| Download cache | Browser default folder → route immediately | Don't leave files in downloads folder |

Do not leave downloaded files in browser default folder — route to appropriate ingest pipeline immediately.

## Fallback Chain

```
1. OneDrive Web (Playwright / hl-onedrive)  (primary — authenticated browse/search)
   ↓ (web UI blocked / loading failure)
2. SharePoint Document Library REST API  (if hl-sharepoint available — same backend)
   ↓ (API unavailable / permission denied)
3. Local macOS OneDrive Sync Folder  (~/Library/CloudStorage/OneDrive-Astemo/)
   ↓ (sync not configured / folder empty / critically outdated)
4. Ask operator  (explicit disclosure: "OneDrive unavailable — please download manually")
```

Always disclose which fallback was used and how old the data is.

## Prerequisites

- **Chrome "Sabarish" profile** — authenticated to `sabarish.duvvuru.qx@astemo.com` M365
- **hl-onedrive patterns** loaded — Playwright browse/download methods
- **astemo-work-context** loaded — for program-specific file classification
- **Network** — Astemo OneDrive may work via Graph API or sync client without VPN; test before assuming VPN required
- **fh4s-ingest** available — for engineering drawing routing

## Error Handling

| Failure | Recovery |
|---------|----------|
| File locked by another user | Disclose who has it open; suggest asking them to close or wait |
| Sync conflict (multiple versions) | Download both, flag for operator to resolve |
| Large file download timeout | Retry with explicit wait; if >100MB, suggest direct download link |
| Permission denied on shared file | Disclose; suggest requesting access from file owner |
| Japanese filename corrupted | Re-extract with UTF-8; preserve original filename exactly |
| Folder not found | List available folders; suggest correct path |

## Contract

- **Do not** modify files on OneDrive directly — download to local, process, then upload only with operator approval.
- **Do not** delete files from OneDrive under any circumstances.
- **Preserve** original filenames exactly — especially Japanese characters, never transliterate or rename.
- **Do not** expose OneDrive URLs or SharePoint paths in customer-facing artifacts.
- **Do not** store file content in SHADOW training pairs — classification metadata only (filename, type, program, date).
- Downloaded files must be routed to ingest pipelines immediately — do not accumulate in downloads folder.
