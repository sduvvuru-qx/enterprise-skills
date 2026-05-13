---
name: Astemo Ingest
description: "Ingest, classify, and organize files into the correct program folder structure. When files are dropped into an ingest folder, analyze content and move to proper location. Cross-program: FH4S, FH4L, XP7G, XY9H, P703. Program resolved via astemo-work-context. ALWAYS clean up ingest after processing — standing rule. Trigger on: ingest, new files, organize, move to proper location, file drop, any mention of files in downloads or ingest folder."
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  replaces: [fh4s-ingest]
  filePattern: '**/ingest/**'
  priority: 65
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-ingest
  icon_style: craft-category-glyph-v1
---

# Astemo Ingest

Cross-program file ingestion. Program resolved at runtime.

## Program Resolution

```
program = astemo-work-context.resolve_program()
paths:
  ingest:    {dropbox_root}/ingest/          (or SHADOW/ASTEMO/{code}/ingest/)
  drawings:  {dropbox_root}/03_Drawings/
  tracker:   {dropbox_root}/02_Change_Point_Tracker/
  meetings:  {dropbox_root}/07_Meeting_Notes/
  quality:   {dropbox_root}/05_Quality_Forms/
  reports:   {dropbox_root}/08_Reports/
  presentations: {dropbox_root}/04_Presentations/
```

## Standard Folder Schema

Per `astemo-project-schema`, every program follows:

```
{dropbox_root}/
├── 00_Inbox/                  ← Alternate drop zone
├── 01_Requirements/
├── 02_Change_Point_Tracker/
├── 03_Drawings/
├── 04_Presentations/
├── 05_Quality_Forms/
├── 06_Schedules/
├── 07_Meeting_Notes/
├── 08_Reports/
├── 09_Standards/
└── 90_Astemo_Config/
```

## Classification Rules

| File Pattern | Destination |
|---|---|
| *change_point*, *ChangePoint*.xlsx | 02_Change_Point_Tracker/ |
| *MLC*, *Honda*.pptx, *.pptm | 04_Presentations/ |
| *Meeting*, *transcript*.txt, *.docx | 07_Meeting_Notes/ |
| *DWG*, drawing *.pdf | 03_Drawings/ |
| *SASG*, *X10*, *K11*, *N10*.xlsx | 05_Quality_Forms/ |
| *schedule*, *Smartsheet*.xlsx | 06_Schedules/ |
| *standard*, *spec*.pdf | 09_Standards/ |

## Workflow

1. `ls -lt {ingest_path}` — check what's there
2. Classify each file by name/extension/content
3. Resolve destination via program folder schema
4. Copy to proper destination (cp, not mv — safer)
5. Verify copies landed
6. Remove originals from ingest/
7. Report what was filed where
8. If PDF drawing → trigger `astemo-smart-drawing` pipeline

## STANDING RULE

**At ANY program level**, if the operator mentions "ingest" or drops files, process immediately. Never leave files sitting in ingest/. Applies to all programs.

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `ingest` / default | "ingest", "new files", "organize" | Classify and move |
| `classify` | "what are these files" | Show classification without moving |
| `clean` | "clean ingest" | Remove processed files |

## Pipeline

```
Ingest folder → Classify → Resolve destination (program schema) → Move → Clean ingest → Report
```

## Error Handling

| Failure | Recovery |
|---|---|
| Cannot classify | Leave in ingest; flag for operator |
| Destination exists | Version with timestamp; do not overwrite |
| Permission denied | Save to ~/Downloads/ temporarily |
| Volume unmounted | Queue classification; process when remounted |
| Smart JSON fails for PDF | File PDF correctly; note extraction failure |

## Contract

- **Cross-program** — resolve paths via `astemo-work-context`.
- **ALWAYS clean up ingest** after processing.
- Never overwrite existing files — version or flag.
- PDF drawings auto-trigger smart JSON extraction.
