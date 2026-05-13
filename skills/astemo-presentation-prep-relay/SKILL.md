---
name: Astemo Presentation Prep Relay
description: Use when preparing Astemo presentation packets, relay-safe Dropbox/A2A handoffs, MLC/PV/customer/internal decks, or operator-approved share-ready packets for FH4S/FH4/FH4L/XY9H work.
metadata:
  depends_on: [astemo-template-fallback,astemo-work-context]
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-presentation-prep-relay
  icon_style: craft-category-glyph-v1
icon: icon.svg
---

# Astemo Presentation Prep Relay

## Purpose

Create or delegate presentation prep packets for Astemo engineering work while respecting program storage, review gates, and data boundaries.

## When triggered

Use for:
- "send relay to start presentation prep"
- "prep the presentation"
- "make a packet"
- MLC/PV/change-point/customer deck preparation
- moving draft material toward Dropbox review/share-ready states

## Workflow

### 1. Resolve context

Before delegation or file creation, identify:

- Program: FH4S, FH4, FH4L, XY9H, XP7G, Ford, Gen IV VA roll-up
- Audience: internal Astemo, Honda, Ford, JP/US, management
- Artifact: meeting packet, deck, one-pager, update slide, notes
- Evidence files: tracker, PV schedule, current deck, meeting notes
- Destination: `04_Presentations/`, `08_Reports/`, or `astemo-ent/relay/`

### 2. Build relay-safe task

Include only:

- purpose
- program
- audience
- source artifact paths using program-relative Dropbox paths when possible
- deliverables requested
- boundaries: prepare only, do not send

Do not include:

- internal Shadow implementation details
- local usernames
- secrets, credentials, login identities
- broad filesystem maps

### 3. Required deliverables

Ask the relay/prep agent for:

1. **1-slide / 1-page opener** — objective + top asks.
2. **Action-capture table** — Item | Owner | Evidence needed | Due date | Risk if late.
3. **Speaking notes** — internal/supplier/customer-safe tone.
4. **Storage state** — draft / needs review / ready to share.
5. **Approval questions** — anything needing operator confirmation before editing/sending.

### 4. Review states

Use this state ladder:

| State | Meaning |
|---|---|
| `10-needs-review` | operator must review before use |
| `00-ready-to-share` | operator approved for intended audience |
| `returned-for-fix` | needs correction before share |
| `archive` | superseded or retained for traceability |

### 5. Output response to operator

Keep response compact:

```md
Relay sent / packet ready.

Packet: [path]
Source artifact: [path]
Status: [needs review / ready]

Approval needed:
- [question]
- [question]

Next action:
- [what operator should do now]
```

## Astemo deck guardrails

- Astemo corporate style by default.
- No invented commitments to Honda/Ford.
- Confirm exact slide number before editing.
- Release/gate closure slides stay in main flow, not appendix, unless explicitly approved.
- For FH4S MLC/PV language: do not imply T3 maturity, Honda approval, or PV usability without QA/SQ approval basis.

## Common source paths

For FH4S:

- tracker: `FH4S_4E0A_IPM/02_Change_Point_Tracker/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx`
- PV schedule: `FH4S_4E0A_IPM/06_Schedules/FH4S_PV_Schedule_2026-03-31.xlsx`
- presentations: `FH4S_4E0A_IPM/04_Presentations/`
- reports/packets: `FH4S_4E0A_IPM/08_Reports/`

For other programs, resolve through program registry/routing first; do not file under FH4S by default.

## Pipeline

```
Resolve Context (program, audience, artifact type, evidence files)
  ↓
Scrub Relay Packet (remove SHADOW internals, local paths, hostnames, secrets)
  ↓
Build Task for Relay Agent (purpose, sources, deliverables, boundaries)
  ↓
Generate Deliverables (1-slide opener, action table, speaking notes, storage state)
  ↓
Review Gate (operator review before share)
  ↓
Route
  ├── Draft → `<program>/04_Presentations/` or `08_Reports/`
  ├── Review packet → `astemo-ent/relay/10-needs-review/`
  ├── Share-ready → `astemo-ent/relay/00-ready-to-share/`
  └── Retracted → `ShadowArchive/90-sensitive-sealed/dropbox-relay-retractions/`
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `prep` / default | "prep the presentation", "make a packet" | Full relay-safe task with deliverables |
| `mlc` | "MLC deck", "MLC slides" | FH4S-specific MLC response deck preparation |
| `pv-alignment` | "PV alignment", "PV status" | Production verification status deck |
| `customer` | "Honda deck", "customer presentation" | External customer-facing packet (extra scrub) |
| `internal` | "internal deck", "CFT update" | Internal-only presentation (standard scrub) |
| `one-pager` | "one-pager", "single slide" | 1-slide/1-page summary with top asks |

Default mode generates a full prep packet with review gate.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Draft presentations | `<program>/04_Presentations/` | Program Dropbox root |
| Draft reports | `<program>/08_Reports/` | Program Dropbox root |
| Review packet | `astemo-ent/relay/10-needs-review/` | Pending operator review |
| Share-ready | `astemo-ent/relay/00-ready-to-share/` | Operator-approved |
| Retracted/unsafe | `ShadowArchive/90-sensitive-sealed/dropbox-relay-retractions/` | Regenerate sanitized |

Resolve `<program>` path via `astemo-work-context` and `PROGRAM-FILE-ROUTING.md`.

## Fallback Chain

```
1. Relay agent (A2A session)  (primary — dedicated prep agent)
   ↓ (no relay session active / relay unavailable)
2. Local generation (current session)  (generate deliverables inline)
   ↓ (session overloaded / complex multi-slide deck)
3. Manual handoff  (operator creates from template, agent provides content/notes)
   ↓ (template unavailable)
4. Ask operator  (explicit disclosure: "Automated prep unavailable — suggest starting from last presentation")
```

Always disclose who/what generated the deliverables.

## Prerequisites

- **astemo-work-context** loaded — for program routing and Dropbox paths
- **PROGRAM-FILE-ROUTING.md** accessible — for confirmed Dropbox roots
- **program-registry.json** available — for program metadata
- **pptx-agent** or **astemo-org-templates** loaded — for deck generation
- **Dropbox sync** active — for cross-device relay staging

## Error Handling

| Failure | Recovery |
|---------|----------|
| Program path unresolved | Use `astemo-work-context` to resolve; ask operator if ambiguous |
| Source artifact missing (tracker, PV schedule) | Flag missing source; proceed with available data; mark gaps |
| Relay agent unavailable | Fall back to local generation; inform operator |
| Unsafe content detected in packet | Move to `90-sensitive-sealed/dropbox-relay-retractions/`; regenerate sanitized |
| Template not found | Use `astemo-template-fallback` — copy existing branded file as base |
| Customer commitment ambiguity | Mark as `needs evidence`; never invent approval language |

## Contract

- **Never** include SHADOW internals in relay packets — no local paths, hostnames, SSH aliases, mesh labels, or debug JSON.
- **Never** invent Honda/Ford commitments — confirm exact language before customer-facing slides.
- **Never** imply T3 maturity, Honda approval, or PV usability without QA/SQ approval basis.
- **Scrub all packets** before Dropbox staging — Dropbox is the Astemo async relay, not a mirror of SHADOW internals.
- **Release/gate closure slides** stay in main flow, not appendix, unless explicitly approved.
- **Confirm exact slide numbers** before editing existing decks.
- Relay packets are **prepare only, do not send** — operator reviews and sends manually.
