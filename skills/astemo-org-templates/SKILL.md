---
name: Astemo Org Templates
description: 'Use when the user needs: Generate, use, or reference the complete Astemo xEV smart-office template suite — PPTX presentations and Excel workbooks for DE, QA, PM, PROC, VV, MFG, and MGMT teams.'
icon: icon.svg
type: implementation
metadata:
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: fh4s-astemo-org-templates
  icon_style: craft-category-glyph-v1
---

# astemo-org-templates

Use this skill whenever building, modifying, or referencing any Astemo document template.
The full template library covers all 7 xEV Inverter teams and includes both PPTX and Excel.

**Design dependency:** Load `astemo-design-system` for all current tokens, PPTX slide-master/layout rules, XLSX styling, and cross-app adapter rules. This skill is a template inventory and generation entrypoint, not the design source of truth.

## Modules

| Module | Location | Purpose |
|--------|----------|---------|
| `astemo_pptx.py` | `tools/shadow_enterprise_cli/astemo_pptx.py` | Core PPTX library — `AstemoPresentation`, colors, layouts |
| `xev_templates.py` | `tools/shadow_enterprise_cli/xev_templates.py` | Meeting-type PPTX templates (6 types) |
| `org_templates.py` | `tools/shadow_enterprise_cli/org_templates.py` | Full org suite — 7 teams × PPTX + Excel |

## Template Library (23 files → `fh4s/templates/<TEAM>/`)

### DE — Design Engineering
| File | Format | Use |
|------|--------|-----|
| `DE_Weekly_Report.pptx` | PPTX | Weekly DE status (programs, drawings, actions) |
| `DE_JP_US_Sync_Bilingual.pptx` | PPTX | JP-US sync (bilingual EN/JP) — Sakamoto-san |
| `DE_Change_Point_Brief_Honda.pptx` | PPTX | Honda MLC response — change point summary |
| `DE_Change_Point_Tracker.xlsx` | Excel | Change points + KC/VP linkage sheet |
| `DE_Drawing_Review_Checklist.xlsx` | Excel | Drawing review sign-off per part |

### QA — Quality Assurance
| File | Format | Use |
|------|--------|-----|
| `QA_Quality_Review.pptx` | PPTX | DFMEA/DRBFM/Linkage status review |
| `QA_MQ_Readiness_Review.pptx` | PPTX | Mass Production Quality gate |
| `QA_DFMEA_K11-03A.xlsx` | Excel | DFMEA tracker (K11-03A) — failure modes, RPN |
| `QA_DRBFM_K11-04A.xlsx` | Excel | DRBFM tracker (K11-04A) — change risk analysis |
| `QA_Linkage_Check_N41-02A.xlsx` | Excel | N41-02A linkage: Drawing→DFMEA→CP→WI |

### PM — Program Management
| File | Format | Use |
|------|--------|-----|
| `PM_Program_Status_Review.pptx` | PPTX | All-program dashboard (FH4S/FH4/FH4L/P703) |
| `PM_Milestone_Schedule_Review.pptx` | PPTX | Gate schedule (DR/TR/MQ/SOP) |
| `PM_Risk_Register.xlsx` | Excel | Cross-program risk register (RPN-scored) |
| `PM_Capacity_Planner.xlsx` | Excel | Engineer allocation % per program |

### PROC — Procurement & Sourcing
| File | Format | Use |
|------|--------|-----|
| `PROC_VEC_Analysis_Report.pptx` | PPTX | VEC opportunity analysis (Honda-facing) |
| `PROC_Supplier_Review.pptx` | PPTX | M25 supplier selection status |
| `PROC_M25_Supplier_Selection.xlsx` | Excel | JP→US supplier comparison + lead time |
| `PROC_VEC_Tracker.xlsx` | Excel | VEC proposals, savings, Honda status |

### VV — Validation & Test
| File | Format | Use |
|------|--------|-----|
| `VV_Test_Validation_Review.pptx` | PPTX | PV test results (Pass/Fail/Pending) |
| `VV_PV_Test_Plan.xlsx` | Excel | PV test plan: spec, result, sign-off |

### MFG — Manufacturing
| File | Format | Use |
|------|--------|-----|
| `MFG_Manufacturing_Review.pptx` | PPTX | US production transfer status (N10-02A) |
| `MFG_Control_Plan_L10-03A.xlsx` | Excel | Control Plan (L10-03A) — KC + measurement |

### MGMT — Management
| File | Format | Use |
|------|--------|-----|
| `MGMT_Management_Review.pptx` | PPTX | Executive dashboard + decisions required |

---

## How to Build Templates

```python
import os, sys
os.chdir("/Volumes/SHADOW/ASTEMO")
sys.path.insert(0, "tools")
from shadow_enterprise_cli.org_templates import build_all, build_team

# Rebuild everything
build_all()

# Rebuild one team
build_team("DE")   # or "QA", "PM", "PROC", "VV", "MFG", "MGMT"
```

Or from the CLI:
```bash
cd /Volumes/SHADOW/ASTEMO
python3 -m shadow_enterprise_cli.org_templates          # all
python3 -m shadow_enterprise_cli.org_templates DE QA    # subset
```

---

## How to Customize a Template

All templates extend `AstemoPresentation`. To create a custom presentation:

```python
import os, sys
os.chdir("/Volumes/SHADOW/ASTEMO")
sys.path.insert(0, "tools")

from shadow_enterprise_cli.astemo_pptx import AstemoPresentation, BLUE, YELLOW, RED, DARK, WHITE, LIGHT_BG
from shadow_enterprise_cli.org_templates import _pptx_status_cols, _pptx_action_slide

with AstemoPresentation("xEV IPM  ·  [Team]") as prs:
    prs.title_slide("Title", "Subtitle", dept="Astemo Americas", date="2026-MM-DD")

    # 3-column status (most common layout)
    _pptx_status_cols(prs, "Status Slide Title", [
        (RED,    "Header Red",    [("Bullet 1", RED), ("Bullet 2", RED)]),
        (YELLOW, "Header Yellow", [("Bullet 1", YELLOW)]),
        (BLUE,   "Header Blue",   [("Bullet 1", BLUE)]),
    ])

    # Table slide
    sl = prs.content_slide("Table Title")
    prs.add_table(sl,
        headers=["Col1", "Col2", "Col3"],
        rows=[["A", "B", "C"]],
        col_widths=[3.0, 3.0, 3.0],
        y=1.05,
        cell_text_colors={(0, 2): RED},   # color specific cells
    )

    # Action items
    _pptx_action_slide(prs, "Actions", [
        ("P0", RED, "Action", "Owner", "Due"),
    ])

    prs.last_slide()
    prs.save("output.pptx")
```

---

## Excel Template Structure

All Excel templates use Astemo branding:
- **Header row**: Astemo Red fill (#B6001A), white bold Arial 11pt
- **Alternating rows**: #F2F2F2 on even rows, white on odd
- **Status cells**: Green=Complete, Yellow=In Progress/At Risk, Red=Open/Hold
- **Auto-filter** on header row, **freeze panes** at row 2
- **Cell border**: thin #CCCCCC on all cells
- **Min column width**: 8 chars

Helpers in `org_templates.py`:
```python
from shadow_enterprise_cli.org_templates import (
    _xl_header_row,   # branded header
    _xl_data_row,     # alternating data row
    _xl_status_cell,  # color-coded status cell
    _xl_freeze_filter, # auto-filter + freeze panes
    _xl_save,         # save workbook
)
```

---

## Rules

1. Always `os.chdir("/Volumes/SHADOW/ASTEMO")` before importing
2. Use `build_team("XX")` to regenerate after edits — don't hand-edit output PPTX/XLSX
3. Section labels follow `"xEV IPM  ·  [Team/Function]"` pattern
4. Bilingual slides (DE/JP-US) always show EN primary, JP secondary in same cell with `/`
5. Status cells: Green=done, Yellow=in-progress/watch, Red=open/blocked
6. RPN color threshold: > 100 = Red, 50-100 = Yellow, < 50 = default

---

## Team Reference

**Team member names, roles, and team assignments are loaded dynamically from shadow-sidecast:**

```bash
# Get US DE team structure and team assignments
shadow-sidecast team-map --program FH4S
shadow-sidecast team-map --program FH4
shadow-sidecast broadcast-org --scope usgrp1
```

**This returns:**
- US DE team members with roles and program assignments
- Cross-functional team members (QA, PM, PROC, VV, MFG, MGMT)
- Team leadership and key stakeholders
- Contact information and reporting lines

| Key | Team | Primary Users | Key Documents |
|-----|------|---------------|---------------|
| DE | Design Engineering | (from shadow-sidecast) | Change Points, Drawings, JP-US Sync |
| QA | Quality Assurance | (from shadow-sidecast) | DFMEA, DRBFM, Linkage Check |
| PM | Program Management | (from shadow-sidecast) | Program Status, Milestones, Risk Register |
| PROC | Procurement & Sourcing | (from shadow-sidecast) | VEC, M25 Supplier Selection |
| VV | Validation & Test | (from shadow-sidecast) | PV Test Plan, Test Review |
| MFG | Manufacturing | (from shadow-sidecast) | Control Plan, Work Instruction |
| MGMT | Management | (from shadow-sidecast) | Executive Dashboard, Decisions |
