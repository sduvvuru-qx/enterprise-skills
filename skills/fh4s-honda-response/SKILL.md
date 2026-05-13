---
name: FH4S Honda Response
description: 'Use when drafting honda customer response materials (PPTX slides, email text, change point summaries) from the FH4S Change Point Tracker. Formats data for Honda consumption: bilingual English/Japanese, professional supplier tone, change points with reasoning. Used for MLC responses, customer presentations, and status updates. Perspective: US DE Lead (from shadow-sidecast team-map --program FH4S) at USGRP1. Trigger on: "Honda response", "prepare for Honda", "slides 24-25", "customer presentation"'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  priority: 85
  bashPattern: honda|mlc|customer
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-honda-response
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# FH4S Honda Response Skill

## Role Context
You are assisting the US DE Lead in preparing materials for Honda — the customer
for the FH4S CR-V Gen4 VA IPM. Honda expects professional, bilingual (EN/JP) communications
from Astemo as their Tier 1 supplier. All materials go through PQA leadership and JP PM
before reaching Honda.

**Leadership and approval contacts are loaded dynamically from shadow-sidecast:**

```bash
# Get leadership and stakeholder information
shadow-sidecast team-map --program FH4S
shadow-sidecast broadcast-org --scope usgrp1
```

**This returns:**
- PQA Lead and PM leadership with roles and program assignments
- JP PM and JP leadership with contact information
- Approval routing and stakeholder chain
- Cross-functional leadership contacts

## Prerequisites
- Load **fh4s-context** for project state, org chart, deadlines
- Load **astemo-xlsx** if generating Excel summaries
- Use **astemo-pptx-slide** skill if generating PPTX slides
- Use **astemo-design-tokens** for brand compliance
- Team mappings from shadow-sidecast: `shadow-sidecast team-map --program FH4S`

## Key Files
| File | Path |
|---|---|
| Change Point Tracker | /Volumes/SHADOW/ASTEMO/fh4s/outputs/workbooks/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx |
| Previous Honda Response | /Volumes/SHADOW/ASTEMO/fh4s/inputs/presentations/IPM_MLC_Response_to_Honda_2026-02-11.pptm |
| Kickoff Deck | /Volumes/SHADOW/ASTEMO/fh4s/inputs/presentations/Gen4VA_IPM_US_Localization_Kickoff_2026-01-15.pptx |

## Output Types

### 1. MLC Response Slides (PPTX)
Honda's MLC (Manufacturing Localization Committee) requires periodic updates on localization progress.

**Slide Structure** (based on Feb 2026 response deck):
- Title slide: "FH4S IPM Localization Status — [DATE]"
- Summary slide: total parts, change points, risk distribution
- Change point detail slides: one per CP or grouped by category
- Schedule slide: key milestones, SOP timeline
- Open items slide: what needs Honda decision/input

**Per Change Point Slide**:
```
[Part Number] [Part Name EN] / [Part Name JP]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Change: [CP Description]
Reason: [Why — cost, lead time, supplier availability, quality improvement]
KA Supplier → IAP Supplier: [old] → [new]
Impact: [Quality/Cost/Delivery assessment]
Status: [Current status]
Risk: [H/M/L with mitigation plan if H]
```

**Slides 24-25** (Ghen's specific request):
- Slide 24: Supplier change summary table (all 14 CPs)
- Slide 25: Risk mitigation plan for High-risk items
- These are priority deliverables for the March 26, 2026 deadline

### 2. Email Response Text
For responding to Honda inquiries or providing status updates:
```
Subject: [FH4S] IPM Localization Status Update — [DATE]

Dear Honda Team,

[Professional opening — reference previous meeting or request]

[Body — organized by topic, bilingual where needed]

[Table of changes if applicable]

[Next steps and timeline]

Best regards,
Sabarish Duvvuru
Design Engineering — IPM
Astemo Americas (USGRP1)
```

### 3. Change Point Summary (Excel)
Filtered view of tracker for Honda consumption:
- Only CP=Yes rows (14 items)
- Columns: Part#, Name (EN/JP), Change Description, Supplier Change, Risk, Status, Target Date
- Use **astemo-xlsx** for Astemo branding
- Save to outputs/workbooks/

## Workflow

### 1. Read Tracker
```python
import openpyxl
wb = openpyxl.load_workbook(TRACKER_PATH)
ws = wb['Changepoints']
# Filter CP=Yes rows
cps = [row for row in ws.iter_rows(min_row=2, values_only=False) if row[4].value == 'Yes']
```

### 2. Assess What Honda Needs
- Check deadline (MLC response date from fh4s-context)
- Determine format: slides, email, or summary table
- If user says "slides 24-25", focus on the supplier change summary and risk mitigation

### 2a. Check The Actual Ask From Mail / Meeting Context

Before rebuilding slides, confirm whether Honda/PMO/Ghen/Miki asked for:

- the full deck
- a specific section
- one change-point summary slide
- a schedule update
- an owner/status clarification

For FH4S IPM localization, do not assume the operator owns the whole MLC deck.
The local/living context often shows a narrower ask such as:

- "update the component change-point slide"
- "reuse the same summary in MLC"
- "explain what is still red vs not DE-owned"

### 2b. Ownership Map Before Slide Work

Map each requested slide or topic into one of four buckets:

- `US DE owns`
- `US DE contributes`
- `PE / BUP / QA / PMO own`
- `remove from this rebuild lane`

For FH4S IPM / Honda MLC, the recurring ownership pattern is:

- `US DE owns`
  - technical change-point definition
  - drawing / part-name alignment
  - DE-safe customer wording
  - confirmed-vs-pending framing for DE items
- `US DE contributes`
  - process-difference explanation where DE is the source
  - customer-safe explanation of what is waiting on others
- `PE / BUP / QA / PMO own`
  - maturity schedule
  - equipment detail package
  - sourcing route / quote / capacity commitments
  - PV condition closure

If the asked-for material spans several owners, prefer a focused US DE packet rather than claiming ownership of the full deck.

### 3. Generate Materials
- Pull data from tracker for each change point
- Format bilingual where Part Name (JP) exists
- Apply professional supplier tone — no internal notes, no draft language
- Include reasoning for every change (Honda expects justification)

### 3a. Focused US DE Packet Option

When the ask is narrower than the whole MLC deck, build a focused packet:

1. title / framing slide
2. US DE change-point summary matrix
3. DE status / next steps
4. optional "outside US DE ownership" slide

This packet should be built from:

- the original Ghen/Miki deck shell
- `change-points.json`
- `dwg-inventory.md`
- Outlook thread context
- local Teams-derived context notes

Preferred matrix columns for Honda-facing DE communication:

- `Part / Drawing Name`
- `What Is Changing`
- `Why`
- `DE Status`
- `Current Next Step`

Bias toward drawing/tracker part names, not ad-hoc shorthand.

### 3b. Original-Source Reconciliation Rule

When local notes, tracker summaries, and deck history disagree, reconcile against the original-source chain in this order:

1. live Outlook thread for the ask and ownership
2. local meeting transcript / Teams-derived notes for discussion context
3. `change-points.json` for current technical status
4. `dwg-inventory.md` for drawing-aligned part naming
5. inherited deck only as layout/shell reference

Do not let inherited deck wording outrank the live ask.

If Outlook says the task is "update the component change-point slide", treat that as the scope anchor even if the old deck contains many adjacent sections.

### 3c. Full-Deck Stop Rule

Stop editing the full inherited MLC deck when all three are true:

1. the ask is narrower than the whole deck
2. multiple sections are owned by PE / BUP / QA / PMO
3. repeated edits are causing slide churn, duplicate content, or trust loss

At that point, switch immediately to a clean focused packet.

### 3d. Screenshot-To-Honda Cadence Artifact

Use this pattern when the operator drops or references a meeting screenshot that contains an Astemo/Honda timing slide, SOP chart, gate schedule, or cross-region support note.

1. Find the actual latest screenshot and inspect it before drafting. Do not reuse an older desktop capture just because it is nearby.
2. Preserve screenshot-derived facts separately from inferred schedule logic. Mark inferred durations as approximations when they are derived from labels such as `16W`, `10W`, or `6 weeks`.
3. Convert the slide into a timeline/cadence artifact, not only a prose summary:
   - standard vs condensed path
   - PV build
   - heat shock / validation
   - cross-section analysis
   - SOP watchpoint
   - IPAAR or approval window
   - customer alignment gate
4. Add a cross-region working lane when AM / JP / Honda alignment is the real issue:
   - `AM risk framing / 米州リスク整理`
   - `JP support owner / 日本側支援責任`
   - `Bilingual evidence pack / 日英資料化`
   - `Honda alignment gate / Honda向け合意`
   - `Customer-safe response / 顧客向け回答`
5. Translate internal coordination into customer-safe Honda wording. Do not expose AM-vs-JP staffing friction. Frame it as scoped support, defined watchpoints, owner/date confirmation, and validation-gate visibility.
6. Apply Japanese corporate review discipline: stable grid, short phase labels, owner/date/action clarity, conservative color, and red only for risk or decision gates.

### 3e. JP / EN Cross-Regional Reply Style

Use this when drafting internal Astemo replies involving US, JP, buyer, sourcing, DE, Honda, B3/Form B, or schedule gates. Avoid long English narrative and avoid synthetic explanatory paragraphs.

Preferred structure:

1. Apology / acknowledgement
2. Current action
3. `Schedule fact` / `事実`
4. `Concern` / `懸念`
5. `Confirmation request` / `確認事項`
6. Interim action if needed

Style rules:

- Use short factual bullets or a compact table.
- Separate confirmed facts from assumptions and requested confirmation.
- Use phrases like `current understanding`, `confirmation request`, and `available evidence`; avoid overconfident claims.
- In Japanese-company internal style, make the logic visible through order and labels, not persuasion.
- Keep wording humble and exact: `I would like to confirm...` is better than `I need to push back...`.
- For deadline disputes, ask whether the date is `final submission` or `interim status checkpoint`.
- For old programs like FH4, explicitly separate missing historical evidence from active FH4S evidence; do not force the FH4S template onto FH4.
- Avoid tool/workflow language in human-facing replies: `source freshness`, `extraction`, `operator correction`, `agent-ready`, `machine-readable`, `live-current`, and `spine`.
- Use corporate substitutes: `schedule basis`, `document reviewed`, `working assumption`, `current version not confirmed`, and `milestone control map`.

Example:

```text
Amanda, apologies for the delayed response.

I am checking the FH4 B3 item now. Before I submit it as final, I would like to confirm the required timing and evidence basis.

Schedule fact:
- FH4 is current mass production.
- FH4 US changeover is planned next July using Line 2.
- FH4S has current priority items due around end of this month.
- FH4S starts first; FH4 Line 2 changeover follows about two months later.

Concern:
FH4 started before the pandemic and before the merger, so I do not have a clean M25-equivalent evidence package to attach for Japan B3 assessment. For FH4, we need to reconstruct evidence from current production, sourcing, and buyer records.

Confirmation request:
Can you confirm if today is the required final FH4 B3 submission deadline, or if today is the checkpoint for status while we gather the FH4 historical / sourcing evidence?
```

### 4. Review Checklist
Before delivering:
- [ ] All 14 change points accounted for
- [ ] Bilingual labels where applicable
- [ ] Risk levels match tracker
- [ ] No internal-only notes leaked (US DE Notes, JP DE Notes are internal)
- [ ] Supplier names are customer-facing versions (not internal codes)
- [ ] Dates are realistic and aligned with DE Activity Schedule
- [ ] Leadership (PQA Lead, JP PM) are CC'd / listed as reviewers (from shadow-sidecast team-map)
- [ ] Team mappings verified via shadow-sidecast (not hardcoded assumptions)
- [ ] The deck scope matches the actual ask; no accidental rebuilding of non-DE-owned sections
- [ ] DE-owned items are separated from sourcing / PE / QA / PMO-owned blockers
- [ ] Live Outlook ask has been checked if the request came from Miki / Ghen / PMO / CFT thread
- [ ] Drawing names on customer-facing DE slides match drawing/tracker naming, not improvised shorthand

### 5. Save Output
- PPTX → `/Volumes/SHADOW/ASTEMO/fh4s/outputs/presentations/`
- Excel → `/Volumes/SHADOW/ASTEMO/fh4s/outputs/workbooks/`
- Email text → `/Volumes/SHADOW/ASTEMO/fh4s/outputs/reports/`
- Naming: `FH4S_Honda_[Type]_YYYY-MM-DD.[ext]`

## Tone Guidelines
- Professional, factual, concise
- Supplier-to-customer register (respectful, not casual)
- Never expose internal risk discussions or cost details unless specifically requested
- Use "Astemo" not "we" in formal documents
- Japanese text should use polite/formal register (です/ます体)
- For internal US-JP Astemo replies, prefer `事実 / 懸念 / 確認事項 / 暫定対応` structure over long English prose.
- Avoid synthetic phrasing such as broad summaries, rhetorical transitions, or over-explaining obvious schedule logic.

## Crystallized From

- Session: 2026-04-22
- Original task: Convert a recent Astemo meeting screenshot into a Mermaid/MMD SOP timing artifact, then refine it for timeline cadence, Astemo design-system rendering, JP/EN cross-region function, and Honda-facing interpretation.
- Pattern extracted: For FH4S/Honda timing screenshots, produce a governed cadence artifact with screenshot facts, approximation caveats, AM/JP/Honda alignment lanes, bilingual labels, and customer-safe support-scope framing.
- Session: 2026-04-22
- Original task: Draft Amanda reply for FH4 B3 / M25 Gen4 PCB timing while correcting FH4 vs FH4S schedule assumptions and avoiding overly English narrative prose.
- Pattern extracted: Cross-regional Astemo replies should use concise JP-style structure: apology, current action, schedule facts, concern, confirmation request, and interim action. Deadline disputes should ask whether the date is final submission or interim checkpoint.
- Session: 2026-04-22
- Original task: Clean up FH4/FH4S schedule dashboard and Amanda-support language after tool-like terms were identified as obvious synthetic tells.
- Pattern extracted: internal Astemo replies and artifacts should be human-first; keep structure for follow-up, but hide generation/process vocabulary.
