---
name: FH4S Change Point Update
description: "FH4S-configured shortcut for astemo-change-point-update. Updates the FH4S Change Point Tracker with meeting/email/transcript data. Prefer astemo-change-point-update (cross-program base); this wrapper auto-sets program=fh4s. Trigger on: update FH4S tracker, FH4S change points, add to FH4S tracker."
icon: icon.svg
metadata:
  depends_on: [astemo-change-point-update,fh4s-context,astemo-xlsx]
  priority: 85
  filePattern: '**/FH4S*Tracker*.xlsx'
  bashPattern: tracker|change.point
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-change-point-update
  icon_style: craft-category-glyph-v1
  replaced_by: astemo-change-point-update
---

# FH4S Change Point Update (wrapper)

Thin wrapper around `astemo-change-point-update` with program pre-set to FH4S.

## What this does

1. Loads `astemo-work-context` with program = `fh4s`
2. Loads `fh4s-context` for FH4S part numbers
3. Delegates to `astemo-change-point-update` workflow

## FH4S-specific paths

| Field | Value |
|---|---|
| Tracker | `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/02_Change_Point_Tracker/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx` |
| Cloud list | `FH4S-Change-Points` on `USGRPxEV` |
| Entity type | `SP.Data.FH4SChangePointsListItem` |
| 57 items tracked | IPM parts for USGRP1 localization |

## Usage

For FH4S work, this wrapper triggers automatically. For other programs, use `astemo-change-point-update` directly.
