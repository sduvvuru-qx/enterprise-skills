---
name: FH4S Meeting Prep
description: "FH4S-configured shortcut for astemo-meeting-prep. Prepares for JP↔US FH4S IPM meetings — tracker, agenda, questions by department. Prefer astemo-meeting-prep (cross-program base); this wrapper auto-sets program=fh4s. Trigger on: FH4S meeting prep, prepare for FH4S meeting, FH4S agenda."
icon: icon.svg
metadata:
  depends_on: [astemo-meeting-prep,fh4s-context]
  priority: 80
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-meeting-prep
  icon_style: craft-category-glyph-v1
  replaced_by: astemo-meeting-prep
---

# FH4S Meeting Prep (wrapper)

Thin wrapper around `astemo-meeting-prep` with program pre-set to FH4S.

## What this does

1. Loads `astemo-work-context` with program = `fh4s`
2. Loads `fh4s-context` for FH4S-specific part numbers and state
3. Delegates to `astemo-meeting-prep` workflow

## FH4S-specific overrides

| Field | Value |
|---|---|
| Program code | `fh4s` |
| Display name | FH4S Gen4 VA IPM |
| Dropbox root | `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/` |
| Tracker | `02_Change_Point_Tracker/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx` |
| Team map | `shadow-sidecast team-map --program FH4S` |
| Smartsheet | ID 2231837406482308 |

## Key people (FH4S)

See `fh4s-context` skill for full org chart, part numbers, and current state.

## Usage

For FH4S work, this wrapper triggers automatically. For other programs, use `astemo-meeting-prep` directly.
