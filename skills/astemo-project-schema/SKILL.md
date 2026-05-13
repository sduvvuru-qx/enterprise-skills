---
name: Astemo Project Schema
description: 'Use when the user needs: Canonical folder schema for Astemo program projects in Dropbox. Instantiate this structure for any new Honda/Nissan/xF program. Ensures consistent layout across FH4S, FH4L, XP7G, XY9H, etc.'
icon: icon.svg
metadata:
  depends_on: [astemo-dropbox,astemo-work-context]
  cluster: astemo-connectors
  domain: project-organization
  applies_to:
  - ~/Library/CloudStorage/Dropbox/
  sibling_skill: astemo-dropbox
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-project-schema
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# astemo-project-schema

Canonical 10-section folder schema for Astemo program Dropbox projects.

## When to Use

- Starting a new program project in Dropbox (FH4L, XY9H, M-XXV, etc.)
- Onboarding a new agent/operator to an existing program
- Auditing Dropbox organization for consistency
- Moving loose files into proper project structure

## Folder Schema

```
{PROGRAM_CODE}_{PART_NUMBER}_{ABBREV}/
├── 01_SASG_Documents/       # Quality standards, SASG forms (X10, K11, N10)
├── 02_Change_Point_Tracker/  # Master tracker xlsx, change-points.json, summaries
├── 03_Drawings/              # PDF drawings, Smart Drawing JSON sidecars
├── 04_Presentations/         # MLC decks, 4M reviews, customer slides
├── 05_Quality_Forms/         # DFMEA, DRBFM, Control Plan, QA sheets
├── 06_Schedules/             # Smartsheet exports, DE Activity schedules
├── 07_Meeting_Notes/         # DE Weekly, EGCFT, CFT, gate reviews
├── 08_Reports/               # PPAP, status reports, context packs
├── 09_Reference/             # Part lists, BOM lookups, supplier info
├── 10_Change_Points_History/ # Archived tracker snapshots (versioned)
└── 90_Astemo_Config/         # Kernel context, operator config, agent state
    └── kernel/
        ├── contexts/          # Per-context files (ipm-us-de, etc.)
        ├── programs/          # Program state JSON, latest notes
        └── operators/         # Per-operator priority files
```

## Naming Convention

- Root folder: `{PROGRAM}_{PART_NUM}_{ABBREV}` (e.g., `FH4S_4E0A_IPM`)
- Use underscores, not spaces or hyphens in folder names
- Date-stamped files: `YYYY-MM-DD-description.ext`
- Program codes: FH4S, FH4L, FH4, XP7G, XY9H, P703, etc.

## Section Purposes

| Section | What Goes In | Who Writes |
|---------|-------------|------------|
| 01_SASG | Standards docs, form templates | DE + QA |
| 02_Change_Point_Tracker | The master tracker (single source of truth) | DE Lead |
| 03_Drawings | PDF + Smart Drawing JSON pairs | DE + agents |
| 04_Presentations | MLC, 4M, gate review decks | DE + PM |
| 05_Quality | DFMEA, DRBFM, Control Plan | DE + QA |
| 06_Schedules | DE Activity, Smartsheet sync | PE + DE |
| 07_Meeting_Notes | Meeting summaries, action items | DE + agents |
| 08_Reports | PPAP packs, status reports, context packs | Agents + DE |
| 09_Reference | Part lists, BOM, supplier data | Agents |
| 10_History | Archived tracker versions (immutable) | Agents (auto) |
| 90_Config | Kernel state, operator priorities, agent context | Agents |

## Instantiation

```bash
PROGRAM="FH4L"
PART="4M0A"
ABBREV="PCU"
BASE=~/Library/CloudStorage/Dropbox
mkdir -p "$BASE/${PROGRAM}_${PART}_${ABBREV}"/{01_SASG_Documents,02_Change_Point_Tracker,03_Drawings,04_Presentations,05_Quality_Forms,06_Schedules,07_Meeting_Notes,08_Reports,09_Reference,10_Change_Points_History,90_Astemo_Config/kernel/contexts}
```

## Current Programs in Dropbox

| Folder | Program | Status | Last Activity |
|--------|---------|--------|---------------|
| `FH4S_4E0A_IPM/` | FH4S Gen4VA IPM | Active | Apr 16 |
| (root) `FH4L_Status_*.pptx` | FH4L | Early stage | Mar 31 |
| (root) `Astemo_AI_Proposal_*.zip` | AI Adoption proposal | One-off | Mar 21 |

## Cross-Device Bridge

These folders sync to the work laptop via Dropbox.
- **Work laptop agent** reads `90_Astemo_Config/` for context
- **JARVIS agent** writes updated tracker JSON, context packs
- Use `astemo-dropbox` skill for staging/pulling between devices

## Gotchas

- Never put screenshots in Dropbox (macOS auto-syncs them) — exclude `Screenshots/`
- RPL debugging or task-specific CSV batches go in `_Archive/`, not project folders
- The `90_Astemo_Config/` section mirrors (and syncs with) the kernel on SHADOW volume
- `_Incoming/` is for triage — process and move files out, don't let them accumulate
- Loose xlsx/docx at Dropbox root = orphans — always route to a project section

## Crystallized From

- Session: 2026-04-16 (JARVIS, Dropbox introspection)
- Original task: Organizing Dropbox for multi-program Astemo work
- Pattern: FH4S_4E0A_IPM 10-section layout generalized to schema


## Pipeline

```
Intent → Resolve target → Execute → Validate → Report
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Standard output | Normal use |
| `verbose` | Detailed diagnostics | Debugging |

## Artifact Routing

- Reports: `ShadowArchive/80-reports/`

## Contract

- Destructive operations require explicit operator confirmation
- Always dry-run before applying changes

