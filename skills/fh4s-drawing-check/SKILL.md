---
name: FH4S Drawing Check
description: 'Use when the user needs: Cross-reference part drawings against the FH4S Change Point Tracker. Load the drawing plan (Gen4VA4s-DWG_plan), check revision currency, verify 2WD/AWD applicability, and flag discrepancies between tracker and drawing records. Perspective: US DE Lead (from shadow-sidecast team-map --program FH4S). Trigger on: "check drawings", "drawing revision", "DWG plan", "cross-check drawings", "verify drawings", "drawing status", "rev check".'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  priority: 80
  filePattern: '**/drawings/**'
  bashPattern: drawing|dwg|revision
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-drawing-check
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# FH4S Drawing Check Skill

## Role Context
You are assisting the US DE Lead (from shadow-sidecast team-map --program FH4S) in managing
drawing currency for the FH4S IPM localization. Drawings originate from JP DE and must be tracked
for revision level, 2WD/AWD applicability, and alignment with the Change Point Tracker.

**JP team structure and roles are loaded dynamically from shadow-sidecast:**

```bash
# Get JP DE team with roles and responsibilities
shadow-sidecast team-map --program FH4S

# Get cross-functional stakeholders
shadow-sidecast broadcast-org --scope usgrp1
```

**This returns:**
- JP DE members with roles and program assignments
- JP Buyers and JP SQ with drawing-related responsibilities
- Contact information and reporting lines
- Role-based drawing ownership (mechanical, electrical, process)

## Prerequisites
- Load **fh4s-context** for part numbers, drawing numbers, org chart

## Key Files
| File | Path |
|---|---|
| Drawing Plan | /Volumes/SHADOW/ASTEMO/fh4s/inputs/drawings/Gen4VA4s-DWG_plan_kmg_BKUP_260225.xlsx |
| Change Point Tracker | /Volumes/SHADOW/ASTEMO/fh4s/outputs/workbooks/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx |
| Drawing PDFs | /Volumes/SHADOW/ASTEMO/fh4s/inputs/drawings/ |
| Lead Frame Drawings | /Volumes/SHADOW/ASTEMO/fh4s/inputs/drawings/lead-frame/ |
| SharePoint Drawings | https://hitachigroup.sharepoint.com/:x:/s/msteams_524615/EWYnS8GMTudHl9WRlc5d884BmkYYEYk2auM66exknQS8bA |

## Drawing Plan Structure
The DWG plan Excel has multiple sheets:
- **2WD** — drawings for 4E0A/2WD variant
- **AWD** — drawings for 4E0A/E-AWD variant
- **4D0A** — (if present) related platform variant

Key columns in DWG plan:
| Col | Header | Purpose |
|---|---|---|
| A | Drawing Number | X30E5-Jxxxx-xxx format |
| B | Part Name | English |
| C | Rev | Current revision level |
| D | Date | Revision date |
| E | Status | Released/Draft/Pending |
| F | Applicability | 2WD/AWD/Both |

## Tracker Drawing Columns (Changepoints sheet)
| Col | Header |
|---|---|
| P | Drawing Number |
| Q | Drawing Rev |
| R | Drawing Date |
| S | 2WD/AWD |

## Workflow

### 1. Load Both Sources
```python
import openpyxl

dwg = openpyxl.load_workbook(DWG_PLAN_PATH)
tracker = openpyxl.load_workbook(TRACKER_PATH)

dwg_2wd = dwg['2WD']  # or whichever sheets exist
dwg_awd = dwg['AWD']
cp_ws = tracker['Changepoints']
```

### 2. Build Drawing Index
Create a lookup from drawing number → {rev, date, status, applicability} from the DWG plan.

### 3. Cross-Reference
For each row in the Changepoints sheet:
1. Get Drawing Number (col P)
2. Look up in DWG plan index
3. Compare:
   - **Rev mismatch**: Tracker rev != DWG plan rev → flag as outdated
   - **Missing drawing**: Part in tracker but no drawing in plan → flag
   - **Applicability mismatch**: Tracker says "2WD" but DWG plan says "AWD" or "Both"
   - **Date gap**: Drawing date significantly older than last tracker update
   - **Status not Released**: Drawing still Draft/Pending for production parts

### 4. Generate Report
```
# FH4S Drawing Cross-Check Report — [DATE]

## Summary
- Parts checked: 57
- Revision mismatches: X
- Missing drawings: X
- Applicability issues: X

## Discrepancies

### Revision Mismatches
| Item# | Part Number | Part Name | Tracker Rev | DWG Plan Rev | DWG Date |
|---|---|---|---|---|---|
| 12 | X30E5-J2047-xxx | Lead Frame | B | C | 2026-02-15 |

### Missing Drawings
| Item# | Part Number | Part Name | Notes |
|---|---|---|---|

### Applicability Issues
| Item# | Part Number | Tracker | DWG Plan | Notes |
|---|---|---|---|---|
```

### 5. Flag Actions
For each discrepancy, recommend action:
- Rev mismatch → "Request latest drawing from role-appropriate JP DE member (see shadow-sidecast)"
- Missing → "Confirm if drawing exists on SharePoint or request from JP DE"
- Applicability → "Verify with JP DE which variants apply"

### 6. Save Output
- Report → `/Volumes/SHADOW/ASTEMO/fh4s/outputs/reports/FH4S_Drawing_Check_YYYY-MM-DD.md`
- If updates needed in tracker, use **fh4s-change-point-update** skill

## Notes
- Top-level drawings: IPM COMP X30E5-J2047-013, PM COMP X30E5-J2047-012
- Role-based drawing ownership (mechanical, electrical, process) is available from shadow-sidecast
- SharePoint is the official drawing repository; local copies may lag
