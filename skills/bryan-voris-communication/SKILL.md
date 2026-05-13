---
name: Bryan Voris Communication
description: Use when drafting, replying to, or analyzing Teams/email communication with Bryan Voris (Bryan) for Astemo program work, especially when adapting tone by role, authority, urgency, and program context.
icon: icon.svg
metadata:
  depends_on: [astemo-colleague-attunement]
  category: enterprise/astemo
  family: enterprise
  lifecycle: active
  canonical_slug: astemo-bryan-voris-communication
  icon_style: craft-category-glyph-v1
---

# Bryan Voris Communication

Read profile first:

`/Volumes/☯Duality/spaces/professional/sduvvuru-qx/team-profiles/bryan-voris.md`

Use `astemo-colleague-attunement` for the general framework.

## Default posture

Department-level escalation, alignment, management visibility, business/customer impact

## Best pattern

```text
Bryan — quick escalation / visibility item for [program].

Bottom line: [impact in one sentence].

Risk:
- [customer/gate/schedule/resource impact]

Current mitigation:
- [what team is doing]

Decision/support needed:
- [specific ask] by [date].
```

## Watchouts

- Do not send unresolved technical noise.
- Do not ask broad “what should we do?” without options.
- Do not bypass Kevin unless escalation path is explicit.

## Pipeline

```
Intent → Audience analysis → Tone selection → Draft → Operator review → Operator sends
```

1. Resolve communication intent (inform, request, escalate, align)
2. Load colleague profile for context
3. Select tone by role relationship and urgency
4. Generate draft following best pattern
5. Present to operator for review and send

## Modes

| Mode | Output | When |
|------|--------|------|
| `draft` (default) | Teams/email draft for operator review | Operator asks to write/reply |
| `analyze` | Tone/style analysis of received message | Operator shares incoming message |
| `translate` | Bilingual JP↔EN adaptation | Cross-cultural message needed |

## Artifact Routing

- Draft messages: presented in chat for operator review — never sent automatically
- Meeting notes derived from communication: `ShadowArchive/80-reports/`
- No artifacts saved for chat-only review mode

## Fallback Chain

1. Load colleague profile from `team-profiles/`
2. Check recent Teams/email history for context
3. Use `astemo-colleague-attunement` for general framework
4. If unsure about current role/project: ask operator before assuming

## Prerequisites

- Chrome "Sabarish" profile with Teams/Outlook authentication
- Knowledge of colleague's role and program involvement
- `astemo-colleague-attunement` skill loaded for tone framework

## Error Handling

- **Wrong audience**: If message content touches multiple stakeholders, flag and ask operator which channel to use
- **Tone mismatch**: If urgency level is ambiguous, default to professional-neutral and flag for operator adjustment
- **Missing context**: If colleague's current project assignment is unknown, ask operator rather than guessing

## Contract

- **NEVER click Send** — always draft → operator reviews → operator sends
- **Preserve Outlook signatures** — do not strip or replace existing signature blocks
- **No invented information** — do not fabricate details about the colleague's schedule, decisions, or project status
- **No agent system references** — keep internal SHADOW/agent/tool references out of message content
- **Operator correction override** — if operator corrects a detail about the colleague, use the corrected version immediately for the rest of the session
