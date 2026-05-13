---
name: FH4S Ingest
description: "FH4S-configured shortcut for astemo-ingest. Ingests and classifies files for the FH4S IPM project. Prefer astemo-ingest (cross-program base); this wrapper auto-sets program=fh4s. Trigger on: FH4S ingest, new FH4S files, organize FH4S files."
icon: icon.svg
metadata:
  depends_on: [astemo-ingest,fh4s-context]
  filePattern: '**/ingest/**'
  priority: 60
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-ingest
  icon_style: craft-category-glyph-v1
  replaced_by: astemo-ingest
---

# FH4S Ingest (wrapper)

Thin wrapper around `astemo-ingest` with program pre-set to FH4S.

## What this does

1. Loads `astemo-work-context` with program = `fh4s`
2. Delegates to `astemo-ingest` workflow with FH4S paths

## FH4S-specific paths

| Field | Value |
|---|---|
| Ingest folder | SHADOW/ASTEMO/fh4s/ingest/ or Dropbox ingest |
| Project root | `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/` |

## Usage

For FH4S work, this wrapper triggers automatically. For other programs, use `astemo-ingest` directly.
