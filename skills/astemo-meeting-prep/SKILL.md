---
name: Astemo Meeting Prep
description: "Prepare for JP↔US program meetings — load tracker, generate agenda by department (JP DE/JP Buyers/JP SQ/JP PE), draft questions with part# and context, create meeting notes template. Cross-program: FH4S, FH4L, XP7G, XY9H, P703. Program resolved via astemo-work-context. Trigger on: meeting prep, prepare for meeting, agenda, what to ask Japan, questions for JP, meeting template, weekly sync, prepare for meeting."
icon: icon.svg
metadata:
  depends_on: [astemo-work-context,astemo-xlsx]
  replaces: [fh4s-meeting-prep]
  priority: 85
  bashPattern: meeting|agenda
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-meeting-prep
  icon_style: craft-category-glyph-v1
---

# Astemo Meeting Prep

Cross-program meeting preparation. Program resolved at runtime via `astemo-work-context`.

## Quick Start

```
1. Load astemo-work-context → resolve program (code, dropbox_root, team_map)
2. Read program-registry.json → tracker_path, meeting_notes_path, reports_path
3. Load {program}-context skill for part numbers, current state
4. Read Change Point Tracker → filter open/high-risk items
5. Generate agenda grouped by JP department
6. Draft questions with part#, context, need-by date
7. Create meeting notes template with attendees from team map
8. Save to {dropbox_root}/08_Reports/
```

## Program Resolution

All paths come from `astemo-work-context` → `program-registry.json`:

```
program = astemo-work-context.resolve_program()  # e.g. "fh4s"

paths:
  tracker:     {dropbox_root}/{tracker_path}
  meetings:    {dropbox_root}/07_Meeting_Notes/
  reports:     {dropbox_root}/08_Reports/
  presentations: {dropbox_root}/04_Presentations/
  smartsheet:  {smartsheet_id} (if set)
  team_map:    shadow-sidecast team-map --program {code}
```

## Workflow

### 1. Load Current State
```
Read the Change Point Tracker → Changepoints sheet
Filter: Status != "Complete", Risk in ("H","M"), any row with open questions
```

### 2. Generate Agenda by Department
Group open items by who owns the answer:

**JP DE**
- Drawing revisions pending, design changes, spec clarifications
- Sub-part supplier changes
- Tracker columns: CP Description, US DE Notes

**JP Buyers**
- Supplier allocation changes, volume splits, quote status
- BOM delta from KA→IAP sourcing
- Tracker columns: Supplier columns, Cost Impact

**JP SQ**
- Quality ranking changes, PPAP requirements, inspection method changes
- Tracker columns: Quality Risk, SQ Notes

**JP PE**
- Process transfer status, equipment readiness, line qualification
- Production schedule alignment

### 3. Draft Questions
For each open item:
```
[Part#] [Part Name] — [Question]
  Context: [What US knows]
  Need by: [Date if applicable]
```

### 4. Create Meeting Notes Template
```
# {ProgramDisplayName} JP↔US Meeting — [DATE]
## Attendees
- US: [from shadow-sidecast team-map --program {code}]
- JP: [from shadow-sidecast team-map --program {code}]
- Interpreter: [from shadow-sidecast team-map --program {code}]

## Agenda Items
1. [item] — Owner: [name]
   Discussion:
   Decision:
   Action:

## Open Actions from Previous Meeting
[Pull from last meeting notes]

## New Action Items
| # | Action | Owner | Due |
|---|---|---|---|

## Next Meeting
Date: [suggest next weekly slot]
```

### 5. Save Output
- Save agenda to `{dropbox_root}/08_Reports/{ProgramCode}_Meeting_Agenda_YYYY-MM-DD.md`
- If Excel requested, use `astemo-xlsx` skill

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `prep` / default | "meeting prep", "prepare for meeting" | Full agenda with questions by department |
| `agenda` | "agenda", "meeting agenda" | Agenda items only |
| `questions` | "what to ask Japan", "questions for JP" | Department-grouped question list |
| `template` | "meeting template", "notes template" | Blank meeting notes template |
| `jp-de` | "JP DE questions" | JP DE-specific questions only |
| `jp-buyers` | "JP Buyer questions" | JP Buyer-specific questions only |
| `jp-sq` | "JP SQ questions" | JP SQ-specific questions only |
| `quick` | "quick prep", "last minute" | Top 5 most urgent items only |

## Pipeline

```
Resolve Program (astemo-work-context → program-registry.json)
  ↓
Load Team Map (shadow-sidecast team-map --program {code})
  ↓
Load Tracker State (filter open/high-risk items)
  ↓
Group by Department (JP DE / JP Buyers / JP SQ / JP PE)
  ↓
Draft Questions (part #, context, need-by date)
  ↓
Generate Meeting Notes Template
  ↓
Route → {dropbox_root}/08_Reports/
```

## Fallback Chain

```
1. Change Point Tracker (local Dropbox-synced copy)
   ↓ (tracker not synced)
2. Excel Online tracker (astemo-excel-online)
   ↓ (SharePoint unavailable)
3. shadow-sidecast last-known state
   ↓ (no cached state)
4. Ask operator
```

## Error Handling

| Failure | Recovery |
|---|---|
| Tracker not found | Check Dropbox sync; fall back to Excel Online |
| shadow-sidecast unavailable | Use static team map from {program}-context |
| No open items found | Verify tracker not filtered; broaden risk filter |
| JP names unresolved | Use generic department labels; flag for sidecast refresh |
| Program not in registry | Ask operator to confirm program code |

## Contract

- **Cross-program** — never hardcode a program name or path. Resolve via `astemo-work-context`.
- **Never** invent JP counterpart responses or commitments — questions only.
- **Never** imply T3 maturity, approval, or PV usability.
- **Group questions** to minimize interpreter burden.
- **Do not** expose internal tracker details in customer-facing agendas.
- **Do not** store meeting content in training pairs — classification metadata only.
