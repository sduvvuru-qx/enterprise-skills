---
name: Astemo Meeting Live Copilot
description: Use when live-capturing Astemo engineering meetings, agenda read-throughs, owner/evidence/due-date actions, or FH4S/FH4/FH4L/XY9H IPM/MLC/PV alignment meeting fragments.
metadata:
  depends_on: [astemo-pipeline,astemo-work-context]
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-meeting-live-copilot
  icon_style: craft-category-glyph-v1
icon: icon.svg
---

# Astemo Meeting Live Copilot

## Purpose

Support Sabarish during live Astemo meetings as a low-friction agenda and action-capture copilot. Do not try to control the meeting. Keep outputs short enough to read while the meeting is happening.

## Trigger posture

Use when the operator says any of:
- "I have both laptops side by side"
- "I'll read the agenda here"
- "live meeting"
- "capture this"
- "are we ready"
- "what should I ask"
- FH4S / MLC / PV / AEP / change point alignment in a meeting context

## Operating mode

1. **Confirm meeting objective in one line.**
2. **Give a 30-second opener.**
3. **Keep a live capture table.**
4. **Convert rough fragments into clean actions.**
5. **Separate facts, assumptions, decisions, and owner asks.**
6. **Never invent customer commitments.**
7. **After meeting, offer a notes/action summary and storage path.**

## Live output template

```md
## Meeting objective
[one sentence]

## Say this
[short opener]

## Ask next
1. [specific ask]
2. [specific ask]
3. [specific ask]

## Capture table
| Item | Owner | Evidence needed | Due date | Risk if late |
|---|---|---|---|---|
```

## Action extraction rules

When the operator pastes/reads rough text:

- If a name is heard, map it to owner only if explicit.
- If no owner is given, use `TBD` and ask who owns it.
- If a date is implied but not confirmed, write `target TBD`.
- If the wording affects Honda/Ford/customer gates, mark as **needs evidence**.
- If the item involves JP DE routing, ask whether to route through Hayakawa-san before parallel follow-up.

## Astemo wording guardrails

Use these safe phrases:

- "owner confirmation"
- "evidence basis"
- "approval / waiver path"
- "I do not want the deck to imply T3 maturity until QA/SQ confirms the basis."
- "This is internal alignment, not a final customer commitment."

Avoid:

- "Honda approved" unless explicitly confirmed.
- "PV usable" unless QA/SQ approval basis is confirmed.
- "T3 equivalent" unless the waiver/approval path is explicitly documented.

## End-of-meeting closeout

Ask for or produce:

```md
## Decisions
- [decision] — owner/date/evidence

## Actions
| # | Action | Owner | Due | Evidence/source |
|---|---|---|---|---|

## Open risks
- [risk] — blocker / next ask
```

If saving is requested, use program routing:
- meeting notes → `<program>/07_Meeting_Notes/`
- status/report packet → `<program>/08_Reports/`
- deck/presentation → `<program>/04_Presentations/`

## Pipeline

```
Confirm Meeting Objective (one line, from operator context)
  ↓
Generate 30-Second Opener (safe supplier tone, no invented commitments)
  ↓
Live Capture (operator reads/pastes fragments → capture table rows)
  ↓
Action Extraction (owner, evidence needed, due date, risk)
  ↓
Closeout
  ├── Decisions table
  ├── Actions table
  ├── Open risks
  └── Route to program storage (07_Meeting_Notes/, 08_Reports/)
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `live` / default | "live meeting", "capture this" | Active capture with live table, opener, ask queue |
| `agenda` | "agenda read-through", "pre-read" | Pre-meeting agenda parse with pre-loaded questions |
| `closeout` | "wrap up", "meeting done" | Decisions + actions + open risks summary |
| `actions-only` | "just the actions" | Extract action items from pasted text without full meeting context |
| `mlc-alignment` | "MLC alignment", "PV alignment" | FH4S-specific MLC/PV gate capture mode |

Default mode is live capture — short outputs, readable during meeting.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Meeting notes | `<program>/07_Meeting_Notes/FH4S_Meeting_YYYY-MM-DD.md` | Durable meeting record |
| Action items | → `astemo-meeting-actions` | Structured action list |
| Status/report packet | `<program>/08_Reports/` | Post-meeting deliverables |
| Deck/presentation | `<program>/04_Presentations/` | If meeting produced slide updates |
| Training pairs | → `astemo-pipeline` | Meeting classification signals only (no customer-sensitive content) |

Resolve `<program>` path via `astemo-work-context` and `PROGRAM-FILE-ROUTING.md`.

## Fallback Chain

```
1. Operator live input (pasting/reading fragments)  (primary — most reliable)
   ↓ (operator cannot share screen/audio)
2. Teams meeting recap (AI summary, if available post-meeting)
   ↓ (no recap / meeting not recorded)
3. Teams chat messages (persistent thread, if meeting had chat)
   ↓ (no chat / external meeting)
4. Operator memory/reconstruction  (explicit disclosure: "No automated source — reconstructing from memory, flag uncertainties")
```

Always disclose capture source and confidence level in the output.

## Prerequisites

- **astemo-work-context** loaded — for program routing and storage paths
- **fh4s-context** loaded (if FH4S meeting) — for org chart, part numbers, JP contacts
- **shadow-sidecast** available — for team mappings (`shadow-sidecast team-map --program FH4S`)
- **Astemo wording guardrails** active — safe supplier phrases, no invented commitments

## Error Handling

| Failure | Recovery |
|---------|----------|
| Operator drops mid-meeting | Save partial capture table; offer to resume when operator returns |
| ASR/garbled input | Ask operator to clarify; mark item as `? unclear` in capture table |
| Unknown owner | Use `TBD` and ask operator to assign |
| Customer commitment ambiguity | Mark as `needs evidence`; never invent Honda/Ford approvals |
| JP term mistranslation | Preserve original JP; flag for interpreter confirmation |
| Program path unknown | Use `astemo-work-context` to resolve; ask operator if ambiguous |

## Post-Meeting Auto-Tracker Pipeline (Priority #1 — Cambrian)

After every meeting closeout, attempt to **auto-propagate actions into the program tracker**.

### Step 1: Classify each action

| Category | Example | Auto-target |
|---|---|---|
| **Change point** | New CP, CP update, CP closure | → `fh4s-change-point-update` → tracker row update |
| **Drawing / PN** | DWG revision, PN assignment, BOM update | → `fh4s-drawing-check` → DWG plan cross-ref |
| **MLC / PV** | PV date, AEP approval, T3 maturation | → `fh4s-mlc-pv-alignment` → MLC slide item |
| **Quality / SASG** | DFMEA entry, DRBFM concern, inspection result | → `fh4s-sasg-forms` → form update |
| **Schedule** | Date change, milestone shift, dependency | → `fh4s-smartsheet-sync` → Smartsheet row |
| **Customer** | Honda ask, customer presentation, AEP discussion | → `fh4s-honda-response` → customer action |
| **Communication** | Email draft, Teams follow-up, JP routing | → `astemo-colleague-attunement` → message draft |

### Step 2: Auto-draft tracker rows

For each classified action, produce a tracker-ready row:

```md
## Auto-Tracker Draft
| # | Category | Action | Owner | Due | CP# (if applicable) | Target Skill |
|---|---|---|---|---|---|---|
| 1 | Change point | [action text] | [owner] | [date] | CP-XX | fh4s-change-point-update |
| 2 | MLC/PV | [action text] | [owner] | [date] | — | fh4s-mlc-pv-alignment |
```

### Step 3: Confirm with operator

> "I've drafted N tracker rows from this meeting. Apply to tracker?"

- If yes → invoke `fh4s-change-point-update` for CP items
- If partial → apply only confirmed rows
- If no → save to `07_Meeting_Notes/` as unprocessed actions

### Step 4: Save meeting notes to Dropbox

Route to `<program>/07_Meeting_Notes/YYYY-MM-DD_<topic>.md` using program routing.

## Action Classification Heuristics

- If text mentions "change point", "CP-", "new supplier", "process change" → **Change Point**
- If text mentions "drawing", "DWG", "revision", "part number", "BOM" → **Drawing/PN**
- If text mentions "PV", "MLC", "AEP", "T3", "maturation", "concession" → **MLC/PV**
- If text mentions "DFMEA", "DRBFM", "SASG", "quality form", "inspection" → **Quality/SASG**
- If text mentions "schedule", "date", "milestone", "SOP", "DR gate" → **Schedule**
- If text mentions "Honda", "customer", "AEP", "slide" → **Customer**
- If text mentions "follow up", "email", "send to", "ask JP" → **Communication**

## Coverage Gap (Cambrian Baseline)

Current coverage: **~35%** of the meeting → tracker → storage pipeline.
Target: **70%** by end of Q2 2026.

Unsolved gaps:
1. ❌ Live audio/ASR capture (operator must paste text)
2. ❌ Auto-extraction of decisions from Teams recap
3. ⚠️ Action → tracker row auto-classification (partially solved above)
4. ⚠️ Cross-session meeting action follow-up (did actions from last week get done?)
5. ❌ Bilingual JP/EN action alignment (JP meeting → EN tracker entries)

## Contract

- **Never** invent customer commitments — use safe phrases only ("owner confirmation", "evidence basis", "approval path").
- **Never** imply T3 maturity, Honda approval, or PV usability without QA/SQ approval basis.
- **Never** expose internal action items, open risks, or decision tables in customer-facing or Dropbox-visible artifacts.
- **Separate** facts, assumptions, decisions, and owner asks — never conflate them.
- **JP DE routing** — ask whether to route through Hayakawa-san before parallel follow-up.
- Meeting captures are **confidential Astemo IP** — do not surface in Honda/customer deliverables.
- **Do not** store customer-sensitive meeting content in SHADOW training pairs — classification metadata only.

## Report export

After meeting capture, generate a branded report from the notes:

```bash
# 1. Extract meeting context into payload format
astemo-render --template meeting-notes meeting-report.json

# 2. Edit the JSON with actual meeting data:
#    - artifact.title: "{PROGRAM} — {Meeting Type} Meeting Notes"
#    - summary: 2-3 sentence recap
#    - evidence: facts from the meeting
#    - decisions: decisions made with owner
#    - actions: extracted action items with priority/owner/due

# 3. Render and deploy
astemo-render meeting-report.json --pdf --pptx --dropbox "08_Reports/{date}-{program}-Meeting-Notes.docx"
```

Or use the auto-extract path:
```bash
# If meeting data is already in JSON format
astemo-render-from-context meeting-data.json --dropbox "08_Reports/meeting-report.docx"
```

The renderer enforces Astemo design system (brandRed headers, status fills, shell anatomy) automatically.
