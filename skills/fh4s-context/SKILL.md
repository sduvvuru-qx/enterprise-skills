---
name: FH4S Context
description: 'Use when the user needs: FH4S IPM project context — key people, part numbers, schedules, and project state. Load this skill whenever working on FH4S to have instant context without re-reading files. Trigger on: "FH4S", "IPM", "change point", "Honda CR-V", "4E0A", "USGRP1", "Gen4 VA", "Greenfield", "Tarboro", any mention of the FH4S project.'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  priority: 95
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-context
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# FH4S IPM Project Context

## Project Identity
- **Product**: Gen4 VA FH4S — 4E0A FHEV IPM COMP / PM COMP
- **Customer**: Honda (HEV CR-V, FH4S platform)
- **Destination**: North America (KA → IAP)
- **JP Production**: Astemo JPMRP1 (Miyagi, Japan)
- **US Production**: Astemo USGRP1 (Greenfield, Indiana) — IPM/PM assembly
- **US Sub-production**: USTBP1 (Tarboro, NC) — PCBA, SMT
- **SOP Target**: Line 1 June 2027, Line 2 July 2027
- **Quality Rank**: A (highest)
- **SASG Control No.**: TA-JPXE-2500003M
- **Drawing Numbers**: IPM: X30E5-J2047-013, PM COMP: X30E5-J2047-012
- **Variants**: 4E0A/2WD and 4E0A/E-AWD (different top-level comp drawings, most sub-parts common)
- **Scope**: IPM only (PCU-level parts like cases, DCDC, reactor are out of scope for your tracker)

## Organization (dynamic via shadow-sidecast)

Team structure is loaded dynamically from shadow-sidecast CLI. Call:
```bash
shadow-sidecast team-map --program FH4S
```

This returns:
- US team members and roles
- Japan team members and roles
- Cross-functional stakeholders
- Meeting cadence
- Production transfer timeline

### Quick Reference (from shadow-sidecast)

**Team structure, roles, and contact information are loaded dynamically from shadow-sidecast:**

```bash
# Get complete team mappings
shadow-sidecast team-map --program FH4S
shadow-sidecast broadcast-org --scope usgrp1
```

**This returns:**
- US Team Leadership (IPM Team Lead, US DE Lead, DE Manager)
- Japan Counterparts (JP DE, JP PE, JP SQ)
- Cross-Functional stakeholders (SQ, BUP, PMO, Sourcing)
- Contact information and reporting lines
- Program assignments and responsibilities

### Support Contact Columns (B1/B2/B3)
Contact assignments are loaded dynamically from shadow-sidecast team-map --program FH4S:

- **B1** = Buyer/Procurement (購買): JP Buyer for JP-sourced; US Buyer for US-sourced
- **B2** = SQ/Quality (品質): JP SQ contact (from shadow-sidecast)
- **B3** = DE Contact (設計): JP DE mechanical contact, JP DE electrical contact (from shadow-sidecast)

### USTBP1 (Tarboro) Note
"USTBP1 is treated as a sub-supplier and the official communication route to USTBP1 is through BUP and SQ." — Kickoff deck

## Key Files
Base path: `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/`

| File | Folder | Purpose |
|---|---|---|
| FH4S_4E0A_IPM_Change_Point_Tracker.xlsx | 02_Change_Point_Tracker/ | CENTRAL tracker (v9, 5 sheets, 47 cols) |
| Gen4VA_IPM_US_Localization_Kickoff_2026-01-15.pptx | 09_Reference/ | Kickoff deck (org chart, schedule, BOM, allocation) |
| IPM_MLC_Response_to_Honda_2026-02-11.pptm | 04_Presentations/ | Honda response deck (42 slides) |
| FH4S_VCU_Chip_Failure_Brief_2026-03-31.pptx | 04_Presentations/ | VCU chip failure investigation brief |
| FH4S_PV_Schedule_2026-03-31.xlsx | 06_Schedules/ | PV schedule (updated 2026-03-31) |
| X10-02A_FormB_Draft_FH4S_IPM_USGRP1.xlsx | 05_Quality_Forms/ | SASG quality activity schedule |
| X10-03A_Rank_of_Quality_Activity_Draft_FormB1_USGRP1_FH4S.xlsx | 05_Quality_Forms/ | Rank of quality activities |
| FH4S_IPM_change_point_US.xlsx | 10_Change_Points_History/ | Base CP file |
| Gen4VA4s-DWG_plan_kmg_BKUP_260225.xlsx | inputs/drawings/ | Drawing plan (2WD/AWD/4D0A sheets) |

## Links
| Resource | URL |
|---|---|
| Smartsheet DE Schedule | https://app.smartsheet.com/sheets/55hj858FpgJwW9mj2Q3XPW7JhqwhGfJm6Pg66H81 |
| SharePoint Drawings | https://hitachigroup.sharepoint.com/:x:/s/msteams_524615/EWYnS8GMTudHl9WRlc5d884BmkYYEYk2auM66exknQS8bA |
| Google Sheets v9 | https://docs.google.com/spreadsheets/d/1nlaMFEFsiw8EF7RowlckdGyribdnfIYt/edit |
| Smartsheet API Token | keychain: service=smartsheet, account=api-token |

## Current State (as of 2026-04-02)
- 57 IPM parts tracked across 47 columns, 5 sheets (Dashboard, Change Log, Changepoints, Meeting Notes, DE Activity)
- 14 change points (CP=Yes), 43 no-change (carry-over)
- Risk: 10 High, 18 Medium, 29 Low
- Honda MLC response **submitted** (was due 2026-03-26) — slides 24-25 finalized ✓
- **P0 — VCU Chip Failure (2026-03-31)**: Diode burn damage in JP production units. First event Dec 2025, second Feb 2026. US first notified Mar 31 (~3-4 month gap, JP acknowledged). Root cause unknown, FTA in progress. Chip change decided (countermeasure). MQ ship ~May 4 — new chip NOT ready, MQ ships with suspect chip. P0 actions: request FTA from JP SQ (from shadow-sidecast) + confirm failure timeline this week.
- Open decisions: MQ ship decision (before May 4), Honda/AP notification plan (before mid-April), chip change PV retest plan (JP info by mid-April)
- SASG document request sent to JP DE 2026-03-22 — response pending (DFMEA, DRBFM, KC/VP designations critical path for DR2)
- PV Schedule updated 2026-03-31

## Chrome Profile
- **sabbu** — default for all general Google/browser work
- **ishuru** — do NOT use for FH4S

## Ingest Rule
When files appear in `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/` or Downloads mentioning FH4S:
1. Copy to ingest folder
2. Classify: presentation→inputs/presentations, excel→inputs/workbooks or change-points, drawing→inputs/drawings, meeting→inputs/meetings
3. Extract and analyze content
4. Move to proper location
5. Clean ingest folder

## Pipeline

```
Load Project State (Change Point Tracker, key files, current issues)
  ↓
Load Team Map (shadow-sidecast team-map --program FH4S)
  ↓
Resolve Current Status (open items, P0 issues, pending decisions)
  ↓
Output
  ├── Context summary (compact chat output)
  ├── Key people + roles → downstream skills
  ├── File paths → downstream skills
  └── Current state → fh4s-meeting-prep, fh4s-change-point-update, etc.
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `full` / default | "FH4S", "FH4S context", "project state" | Full project context — identity, org, files, links, current state |
| `quick` | "quick FH4S", "brief status" | Current state summary only — open items, P0s, pending decisions |
| `people` | "who's on FH4S", "FH4S team" | Team structure and roles from shadow-sidecast |
| `files` | "FH4S files", "where is tracker" | Key file paths and locations |
| `status` | "FH4S status", "where we stand" | Current state section only — change points, risks, open decisions |
| `p0` | "P0", "VCU chip", "critical issues" | P0/critical issue status |

Default output is full project context for first load in a session.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Context summary | Chat output only | Transient — reloaded per session |
| Team map | In-memory → chat | Dynamic from shadow-sidecast |
| File paths | Passed to downstream skills | Not persisted independently |

This skill produces **session context**, not durable artifacts. Downstream skills handle their own artifact routing.

## Fallback Chain

```
1. shadow-sidecast team-map --program FH4S  (primary — live dynamic data)
   ↓ (shadow-sidecast unavailable)
2. Quick Reference section in this skill  (static fallback — may be outdated)
   ↓ (major org changes suspected)
3. ASTEMO-CONTEXT.md §3-§4  (human-maintained org tables)
   ↓ (all sources stale)
4. Ask operator  (explicit disclosure: "Team data may be stale — please confirm key contacts")
```

Always disclose whether team data came from shadow-sidecast (live) or static fallback.

## Prerequisites

- **shadow-sidecast CLI** available — for dynamic team mappings
- **astemo-work-context** loaded first — for cross-program routing
- **Change Point Tracker** synced to Dropbox — for current state
- **Chrome "sabbu" profile** — default for all FH4S browser work (NOT "ishuru")

## Error Handling

| Failure | Recovery |
|---------|----------|
| shadow-sidecast unavailable | Use static Quick Reference; note data may be stale |
| Tracker not synced to Dropbox | Fall back to Excel Online (astemo-excel-online); note sync lag |
| Program registry mismatch | Verify against PROGRAM-FILE-ROUTING.md; ask operator if ambiguous |
| P0 issue without update | Flag as stale; suggest checking with JP SQ/DE directly |
| Dropbox volume unmounted | Note paths are inaccessible; ask operator to mount volume |

## Contract

- **FH4S-only** — other programs should load via `astemo-work-context` first.
- **Do not** expose P0/critical issue details (VCU chip failure, etc.) in customer-facing artifacts.
- **Do not** store project state in SHADOW training pairs — classification metadata only.
- **Chrome "sabbu"** is the only profile for FH4S — **never use "ishuru"**.
- **Do not** infer org changes from partial information — wait for shadow-sidecast or operator confirmation.
- Current state is a **point-in-time snapshot** — always check date before relying on it.
