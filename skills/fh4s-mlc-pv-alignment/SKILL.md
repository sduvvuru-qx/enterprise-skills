---
name: FH4S MLC PV Alignment
description: Use when working on FH4S Gen4VA IPM MLC/PV alignment, Honda AEP approval, <T3 maturation, MLC slide updates, M25 drawing/part-number readiness, or concession closure.
metadata:
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-mlc-pv-alignment
  icon_style: craft-category-glyph-v1
icon: icon.svg
---

# FH4S MLC PV Alignment

## Purpose

Support FH4S Gen4VA IPM alignment work where change points, MLC deck language, PV schedule, Honda AEP approval, and quality/concession evidence intersect.

## Core stance

This workflow is for **internal Astemo owner/evidence confirmation** unless the operator explicitly asks for customer-facing material. Do not treat internal alignment as Honda approval.

## Primary questions

Always resolve these before editing slides or drafting customer language:

1. **PV / Honda AEP for `<T3` maturation**
   - Which parts/tests are affected?
   - Is Honda approval needed before PV start or before result acceptance?
   - Is an internal quality waiver needed?
   - What evidence can be shown if Honda asks?

2. **MLC slide update**
   - What exact slide number/title is being updated?
   - Is slide 25 truly the target, or is the target an IPM change-point summary slide elsewhere?
   - Who requested the update: Miki/PMO, Honda, DE, QA, PE?

3. **M25 DWG / PN list**
   - Does Iwata-san/Sugishita-san own final JP DE input?
   - Should follow-up route through Hayakawa-san?
   - What drawing numbers, part numbers, revision basis, and B2/B3 inputs are missing?

4. **IPM concession 5-fix closure**
   - Do any concession items affect PV or MLC language?
   - What evidence is required: waiver, layout inspection, maturation evidence, SASG rank, KC/VP classification?

## Meeting opener

```text
I want to keep today focused on owner confirmation. I am not trying to make a final Honda commitment in this meeting.

The output I need is a table of owner, evidence, and due date for each open item so PMO and DE can update the MLC / PV package without overstating maturity.
```

## Capture table

```md
| Item | Owner | Evidence needed | Due date | Risk if late |
|---|---|---|---|---|
| PV / Honda AEP `<T3` | TBD | Honda AEP status; PV decision; internal QA position | TBD | PV/MLC language may overstate maturity |
| Affected PV parts | TBD | Part/test list from PV schedule | TBD | AEP discussion too general |
| MLC slide update | Miki / Sabarish / TBD | Correct slide number + approved content | TBD | Wrong slide updated |
| M25 DWG / PN list | Iwata / Sugishita / Hayakawa route | Final DWG/PN list + revision basis | TBD | M25 readiness blocked |
| IPM concession 5 fixes | Emily / SQ / TBD | waiver, inspection, maturation, SASG, KC/VP evidence | TBD | quality gate / PV use blocked |
```

## Safe wording

Use:

- "approval / waiver basis is pending confirmation"
- "owner and evidence path to be confirmed"
- "do not imply T3 maturity until QA/SQ confirms"
- "internal alignment only, not final customer commitment"

Avoid unless explicitly confirmed:

- "Honda approved"
- "PV usable"
- "T3 equivalent"
- "no impact to PV"
- "MLC slide finalized"

## Source files

For FH4S, check:

- `FH4S_4E0A_IPM/02_Change_Point_Tracker/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx`
- `FH4S_4E0A_IPM/06_Schedules/FH4S_PV_Schedule_2026-03-31.xlsx`
- `FH4S_4E0A_IPM/04_Presentations/IPM_MLC_Response_to_Honda_2026-04-17_Rev7_Draft_Awaiting_Feedback.pptm`
- `FH4S_4E0A_IPM/08_Reports/` for synthesized packets

## After meeting

Produce:

```md
## Decisions
- [decision] — owner/date/evidence

## Actions
| # | Action | Owner | Due | Evidence/source |
|---|---|---|---|---|

## Open risks
- [risk] — blocker / next ask

## Slide update implications
- [slide number/title] — [change needed] — [owner approval]
```


## Pipeline

```
Intent → Resolve target → Execute → Validate → Report
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Standard output | Normal use |
| `verbose` | Detailed diagnostics | Debugging |

## Coverage Expansion (Cambrian Priority #4)

Current coverage: **~30%** of MLC/PV alignment workflow automated.
Target: **70%** by end of Q2 2026.

### Unsolved workflows

| Workflow | Current State | Target | Blocker |
|---|---|---|---|
| MLC slide content draft | Manual per-session | Template-driven auto-draft | Need slide layout map |
| PV schedule cross-ref | Manual Excel lookup | Auto-extract from PV schedule | Need PV schedule parse |
| T3 maturation tracker | Not automated | Per-part maturation status | Need evidence taxonomy |
| AEP approval status | Manual email/Teams check | Auto-classify AEP state | Need email parsing |
| Concession → MLC language | Manual | Auto-map concession fixes to MLC language | Need concession DB |
| JP DE input status | Manual follow-up | Track JP input completeness | Need Hayakawa routing |

### MLC Slide Layout Map (To Be Built)

When the operator provides the MLC deck or slide numbers, build a slide-level template:

```json
{
  "slide": 25,
  "title": "IPM Change Point Summary",
  "sections": ["status_table", "evidence_table", "risk_summary"],
  "source_tracker_cols": ["CP#", "Status", "JP DE Notes", "US DE Notes", "Evidence"],
  "auto_fill_possible": true
}
```

### PV Schedule Parse Target

Parse `FH4S_PV_Schedule_*.xlsx` to extract:
- Part/test matrix with dates
- PV phases (PV1, PV2, PV3)
- <T3 items requiring Honda AEP
- Dependency chain: which PV tests gate which MLC decisions

### Concession Cross-Ref Pipeline

For each IPM concession item:
1. Check if concession fix affects MLC language
2. Map concession fix → PV test implication
3. If PV implication exists → flag for MLC slide update
4. If no PV implication → close for MLC purposes, note in tracker

## Artifact Routing

- Reports: `ShadowArchive/80-reports/`
- MLC drafts: `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/04_Presentations/`
- PV analysis: `~/Library/CloudStorage/Dropbox/FH4S_4E0A_IPM/08_Reports/`

## Contract

- Destructive operations require explicit operator confirmation
- Always dry-run before applying changes
- Never imply Honda approval or PV usability without QA/SQ confirmation
- MLC/PV alignment outputs are internal Astemo IP unless explicitly routed to customer-facing

