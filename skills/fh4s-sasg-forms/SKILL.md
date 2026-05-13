---
name: FH4S SASG Forms
description: 'Use when working with SASG quality management forms for the FH4S IPM project. Covers X10-02A FormB (quality schedule), X10-03A (Rank of Quality Activity), K11-03A (DFMEA), K11-04A (DRBFM), and N10-02A (production transfer risk designation). These forms are part of Astemo''s SASG quality system and must be maintained for the FH4S localization. Perspective: US DE Lead (from shadow-sidecast team-map --program FH4S) at USGRP1. Trigger on: "SASG", "quality form", "DFMEA", "DRBFM", "quality schedule", '
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  priority: 75
  filePattern: '**/X10*.xlsx,**/K11*.xlsx,**/N10*.xlsx,**/SASG*'
  bashPattern: sasg|dfmea|drbfm|quality.form
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-sasg-forms
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# FH4S SASG Forms Skill

## Role Context
You are assisting the US DE Lead (from shadow-sidecast team-map --program FH4S) with SASG (Supplier and Site Assurance of Global)
quality management documentation for the FH4S IPM localization. SASG forms are mandatory for
production transfer projects at Astemo. Quality Rank is A (highest) for this project.

## Prerequisites
- Load **fh4s-context** for SASG control number, quality rank, org chart
- Load **astemo-xlsx** for any Excel form updates

## SASG Reference
- **Control No.**: TA-JPXE-2500003M
- **Quality Rank**: A
- **Project**: Gen4 VA FH4S — 4E0A FHEV IPM COMP / PM COMP
- **Transfer**: JPMRP1 (Miyagi) → USGRP1 (Greenfield) + USTBP1 (Tarboro, PCBA)

## Forms Inventory

### X10-02A FormB — Quality Activity Schedule
**File**: `/Volumes/SHADOW/ASTEMO/fh4s/inputs/workbooks/X10-02A_FormB_Draft_FH4S_IPM_USGRP1.xlsx`
**Purpose**: Master schedule of all quality activities required for production transfer
**Content**:
- Activity list with responsible parties (DE, PE, SQ, BUP)
- Target dates and actual completion dates
- Gate reviews: DR1 → DR2 → DR3 → SOP
- Status tracking per activity
**DE Responsibilities**: Drawing review, DFMEA, DRBFM, design verification test plan

### X10-03A — Rank of Quality Activity
**File**: Reference from kickoff deck or SASG system
**Purpose**: Determines quality activity intensity based on project rank (A/B/C)
**For Rank A**: All quality activities mandatory, no skip/defer allowed
- Full DFMEA required
- Full DRBFM required
- Production trial mandatory
- Customer audit preparation required

### K11-03A — DFMEA (Design Failure Mode and Effects Analysis)
**File**: To be created or updated in `/Volumes/SHADOW/ASTEMO/fh4s/outputs/workbooks/`
**Purpose**: Systematic analysis of potential design failure modes for localized parts
**Structure**:
| Column | Content |
|---|---|
| Item/Function | Part function in IPM assembly |
| Potential Failure Mode | What could go wrong |
| Potential Effect | Impact on IPM/vehicle |
| Severity (S) | 1-10 scale |
| Potential Cause | Root cause |
| Occurrence (O) | 1-10 scale |
| Current Controls | Detection/prevention |
| Detection (D) | 1-10 scale |
| RPN | S × O × D |
| Recommended Action | Mitigation |
| Responsible | Owner and target date |

**Focus Areas for FH4S**:
- Supplier changes (14 change points) — new supplier quality risk
- Process changes at USGRP1 vs JPMRP1
- Material changes (if any supplier uses different sub-materials)
- Environmental differences (humidity, temperature at Greenfield vs Miyagi)

### K11-04A — DRBFM (Design Review Based on Failure Mode)
**File**: To be created or updated in `/Volumes/SHADOW/ASTEMO/fh4s/outputs/workbooks/`
**Purpose**: Review design changes and identify concerns from change points
**Structure**:
| Column | Content |
|---|---|
| Change Point | What changed (from tracker CP=Yes items) |
| Reason for Change | Why the change was made |
| Concern | What could go wrong due to the change |
| Countermeasure | How to address the concern |
| Verification | How to confirm countermeasure works |
| Status | Open/Closed |

**Key principle**: Every CP=Yes item in the tracker should have a corresponding DRBFM entry.

### N10-02A FormA — Production Transfer Risk Designation
**File**: `/Volumes/SHADOW/ASTEMO/fh4s/inputs/workbooks/SASG_N10-02A_E_formA_US.pdf`
**Purpose**: Risk assessment for transferring production from JP to US
**Content**:
- Product complexity assessment
- Process difficulty rating
- Supplier readiness evaluation
- Workforce skill gap analysis
- Equipment/tooling transfer plan

## Workflow

### Reading/Analyzing Forms
1. Load the requested form from inputs/workbooks/
2. Parse structure (these are often complex multi-sheet Excel files with merged cells)
3. Identify gaps: empty required fields, missing dates, unsigned approvals
4. Cross-reference with Change Point Tracker for consistency

### Creating/Updating DFMEA
1. Load Change Point Tracker → get all 57 parts
2. For CP=Yes parts (14): create detailed failure mode entries
3. For CP=No parts: create baseline entries (carry-over risk is lower but not zero)
4. Calculate RPN values
5. Flag any RPN > 100 as requiring action plan
6. Use **astemo-xlsx** for formatting
7. Save to outputs/workbooks/

### Creating/Updating DRBFM
1. Load Change Point Tracker → filter CP=Yes (14 items)
2. For each change point:
   - Copy CP Description as Change Point
   - Research concern from US DE Notes, JP DE Notes
   - Propose countermeasure based on standard quality practices
   - Define verification method
3. Use **astemo-xlsx** for formatting
4. Save to outputs/workbooks/

### Updating Quality Schedule (FormB)
1. Load X10-02A FormB
2. Update status of DE-owned activities
3. Update dates based on current project state
4. Flag overdue items
5. Save updated version

## Cross-References
- **fh4s-change-point-update**: CP=Yes items feed directly into DFMEA/DRBFM
- **fh4s-context**: SASG control number, quality rank, schedule dates
- **astemo-xlsx**: All forms must use Astemo branding when regenerated
- Change Point Tracker cols AI-AQ contain SASG form cross-references

## Coverage Expansion (Cambrian Priority #4)

Current coverage: **~25%** of SASG form workflow automated.
Target: **60%** by end of Q2 2026.

### DFMEA Auto-Generation from Change Points

Pipeline:
```
Change Point Tracker (CP=Yes items)
  ↓
For each CP:
  1. Map CP category → failure mode template
  2. Look up JP DE Notes + US DE Notes for cause context
  3. Generate: Item, Failure Mode, Effect, Severity (S)
  4. Generate: Cause, Occurrence (O), Controls, Detection (D)
  5. Calculate RPN = S × O × D
  6. If RPN > 100 → auto-propose recommended action
  7. Cross-ref with fh4s-smart-drawing for part geometry context
  ↓
Output: DFMEA worksheet ready for astemo-xlsx formatting
```

### DFMEA Failure Mode Templates by CP Category

| CP Category | Typical Failure Mode | Severity Range | Key Controls |
|---|---|---|---|
| New supplier | Supplier quality variation, sub-material change | 6-8 | IQA, first article inspection |
| Process change | Dimensional OOS, surface defect | 5-7 | Process FMEA, trial run |
| Material change | Property drift, compatibility | 6-8 | Material test, incoming inspection |
| Design change | Fit/function failure, interference | 5-8 | Design review, mockup test |
| Equipment change | Setup variation, repeatability | 4-6 | Equipment qualification, Cpk study |

### DRBFM Auto-Generation from Change Points

Pipeline:
```
Change Point Tracker (CP=Yes items)
  ↓
For each CP:
  1. Copy CP Description → Change Point column
  2. Copy CP Reason → Reason for Change column
  3. Generate Concern from category + JP/US DE Notes
  4. Generate Countermeasure from standard quality practices
  5. Generate Verification method
  6. Status = Open (default)
  ↓
Output: DRBFM worksheet ready for astemo-xlsx formatting
```

### DRBFM Concern Templates

| Change Type | Default Concern | Default Countermeasure | Verification |
|---|---|---|---|
| New supplier | Supply quality may not match JP baseline | Supplier audit + first article inspection | IQA result, dimensional report |
| Process change | US process may produce different output | Process trial + Cpk verification | Trial run data, SPC |
| Material change | Sub-material may have different properties | Material qualification test | Test report, comparison to JP spec |
| Dimensional change | Tolerance stack may shift at USGRP1 | Mockup build + measurement | Layout inspection |
| Equipment change | New equipment setup variation | Equipment qualification (IQ/OQ/PQ) | Qualification report |

### Quality Schedule (FormB) Auto-Status

Pipeline:
```
X10-02A FormB
  ↓
1. Parse DE-owned activities
2. For each activity:
   - Check tracker/MLC/PV for completion evidence
   - If evidence exists → mark actual date
   - If overdue and no evidence → flag red
   - If upcoming within 2 weeks → flag yellow
3. Generate status summary
  ↓
Output: Updated FormB + status summary
```

## Cross-Session Quality Tracker

After each SASG form update, write a status snapshot:

```json
{
  "date": "2026-05-04",
  "form": "K11-03A",
  "entries_added": 5,
  "rpn_above_100": 2,
  "high_rpn_items": ["CP-07", "CP-12"],
  "coverage_pct": 35,
  "next_session_target": "Complete CP=Yes items 6-14"
}
```

Save to `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/90_Astemo_Config/sasg-progress.json`

## Notes
- SQ lead (from shadow-sidecast team-map --program FH4S) owns the SQ portions of SASG forms
- PQA lead (from shadow-sidecast team-map --program FH4S) coordinates overall SASG compliance
- SQ manager (from shadow-sidecast team-map --program FH4S) reviews and approves quality forms
- BU Quality Head (from shadow-sidecast broadcast-org --scope usgrp1) is final approver for Rank A projects
- USTBP1 (Tarboro) PCBA quality is managed through BUP/SQ, not directly
