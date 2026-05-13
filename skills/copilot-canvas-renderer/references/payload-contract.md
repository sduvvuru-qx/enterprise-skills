# Copilot Canvas Renderer — Payload Contract v2

Version: 2026-05-07
Status: verified (pixel-level 9/9 compliance on DOCX + PDF)

## JSON payload structure

Copilot Chat/Pages produces this JSON in a code block. The renderer reads it and produces deterministic Astemo-branded DOCX and PDF output.

```json
{
  "artifact": {
    "title": "string (required) — becomes DOCX title in brandRed 28pt",
    "audience": "engineering | management | supplier | customer | mixed",
    "program": "string (required) — FH4S | FH4L | XP7G | XY9H | P703 | other",
    "owner": "string — responsible person, romanji for JP names",
    "status": "draft | review | final | approved",
    "date": "YYYY-MM-DD — defaults to today if omitted",
    "classification": "Internal | Confidential | Restricted"
  },
  "summary": "string — 3-5 line evidence-based executive summary",
  "evidence": [
    {
      "fact": "string — what is known (≤80 chars preferred)",
      "source": "string — where it came from",
      "confidence": "high | medium | low",
      "notes": "string — additional context or 'Needs confirmation'"
    }
  ],
  "decisions": [
    {
      "decision": "string — what was decided",
      "basis": "string — why",
      "owner": "string — who owns it",
      "status": "complete | in_progress | open | not_started"
    }
  ],
  "risks": [
    {
      "risk": "string — risk description",
      "impact": "high | medium | low",
      "mitigation": "string — mitigation plan",
      "owner": "string — risk owner"
    }
  ],
  "actions": [
    {
      "priority": "P0 | P1 | P2",
      "action": "string — what needs to be done",
      "owner": "string — who",
      "due": "YYYY-MM-DD | ASAP",
      "status": "complete | in_progress | open | not_started"
    }
  ]
}
```

## Field requirements

| Field | Required | Notes |
|---|---|---|
| `artifact.title` | **Yes** | Becomes DOCX title in brandRed, 28pt bold |
| `artifact.program` | **Yes** | Maps to program routing, metadata table |
| `artifact.audience` | No | Appears in metadata table |
| `artifact.owner` | No | Appears in metadata table. Default "—" |
| `artifact.status` | No | Default "draft". Uppercased in output |
| `artifact.date` | No | Default today (YYYY-MM-DD) |
| `artifact.classification` | No | Default "Internal". Appears in header + metadata |
| `summary` | No | If present → "Executive Summary" section |
| `evidence` | No | Array → 4-column table (Fact, Source, Confidence, Notes) |
| `decisions` | No | Array → 4-column table (Decision, Basis, Owner, Status) |
| `risks` | No | Array → 4-column table (Risk, Impact, Mitigation, Owner) |
| `actions` | No | Array → 5-column table (Priority, Action, Owner, Due, Status) |

## Column width presets

Tables use fixed column widths in DXA (1/20 point). Usable width = 10080 DXA.

| Table | Columns | Widths (DXA) | Ratios |
|---|---|---|---|
| Metadata | 2 | 2520, 7560 | 25/75% |
| Evidence | 4 | 3780, 2520, 1680, 2100 | 37/25/17/21% |
| Decisions | 4 | 3780, 2520, 1680, 2100 | 37/25/17/21% |
| Risks | 4 | 3360, 1260, 3360, 2100 | 33/13/33/21% |
| Actions | 5 | 1080, 3360, 1680, 1680, 2280 | 11/33/17/17/23% |

## Fill mapping

### Status fills (decisions, actions, confidence)

| Value | Background | Text | Use case |
|---|---|---|---|
| `complete` | `#C6EFCE` | `#497F37` | Done, verified |
| `in_progress` | `#FFEB9C` | `#4D4D4D` | Active work underway |
| `open` | `#FFC7CE` | `#B6001A` | Needs action, not started |
| `not_started` | `#D9D9D9` | `#737373` | Planned, not yet begun |
| `on_track` | `#BDD7EE` | `#2A579A` | Tracking to plan |

### Impact fills (risks)

| Value | Background | Text |
|---|---|---|
| `high` | `#FFC7CE` | `#B6001A` |
| `medium` | `#FFEB9C` | `#4D4D4D` |
| `low` | `#D9D9D9` | `#737373` |

### Priority text colors (actions)

| Value | Color | Meaning |
|---|---|---|
| `P0` | `#B6001A` | Critical, blocks others |
| `P1` | `#2A579A` | Important, this week |
| `P2` | `#737373` | Standard, scheduleable |

### Table header style

All table headers: **brandRed `#B6001A` fill with white `#FFFFFF` text**. Bold. 10pt.

This is the defining Astemo visual signal. Gray/blue/black headers are incorrect.

## Shell elements

Every page (including overflow) must have:

1. **Header**: Classification label (brandRed bold) + "| Astemo, Ltd." (muted) + red rule below
2. **Footer**: Thin gray rule + "© Astemo, Ltd. All rights reserved. Page N of M"
3. **Title** (page 1 only): brandRed 28pt bold + 3pt red rule below

## JP name handling

Use romanji in payloads: `Hayakawa-san`, `Saitoh-san`, `Hosoya-san`, etc.
DOCX styles include Yu Gothic UI as CJK fallback font.
For full kanji, add CJK TTF to the reportlab font registry.

## Pipeline commands

```bash
# Single render
NODE_PATH=/tmp/node_modules node scripts/render-astemo-docx.cjs payload.json output.docx
python3 scripts/render-astemo-pdf.py payload.json output.pdf

# Full pipeline
bash scripts/pipeline.sh payload.json --pdf --open
bash scripts/pipeline.sh payload.json --pdf --dropbox 08_Reports/report.docx

# Visual regression test
python3 scripts/visual-regression-test.py
python3 scripts/visual-regression-test.py --sample custom-payload.json
```
