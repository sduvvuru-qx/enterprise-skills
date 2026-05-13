---
name: Hosoya Haruyasu Communication
description: Use when drafting, replying to, or analyzing Teams/email communication with Haruyasu Hosoya (Hosoya-san) for Astemo program work, especially when adapting tone by role, authority, urgency, and program context.
icon: icon.svg
metadata:
  depends_on: [astemo-colleague-attunement]
  category: enterprise/astemo
  family: enterprise
  lifecycle: active
  canonical_slug: astemo-hosoya-haruyasu-communication
  icon_style: craft-category-glyph-v1
---

# Haruyasu Hosoya Communication

Read profile first:

`/Volumes/☯Duality/spaces/professional/sduvvuru-qx/team-profiles/hosoya-haruyasu.md`

Use `astemo-colleague-attunement` for the general framework.

## Default posture

JP PE alignment, line schedules, process/production engineering timing, draft schedule sharing

## Best pattern

```text
Hosoya-san — sharing draft DE schedule for [Line 1/Line 2/topic].

Draft status:
- [item/date]

Open confirmation:
- [specific PE/timing point]

Please correct anything that does not match the JP PE plan.
```

## Watchouts

- Do not present draft as final.
- Do not mix DE design decisions with PE production timing without labels.

## Pipeline

```
Intent → Audience analysis → Bilingual tone selection → Draft → Operator review → Operator sends
```

1. Resolve communication intent (inform, request, escalate, align)
2. Load colleague profile for JP context
3. Select tone considering cross-cultural JP↔US norms
4. Generate draft following best pattern with bilingual consideration
5. Present to operator for review and send

## Modes

| Mode | Output | When |
|------|--------|------|
| `draft` (default) | Teams/email draft for operator review | Operator asks to write/reply |
| `analyze` | Tone/style analysis of received message | Operator shares incoming message |
| `translate` | Bilingual JP↔EN adaptation | Cross-cultural message needed |
| `cultural-check` | Cross-cultural appropriateness review | Before sending to JP colleague |

## Artifact Routing

- Draft messages: presented in chat for operator review — never sent automatically
- Meeting notes derived from communication: `ShadowArchive/80-reports/`
- No artifacts saved for chat-only review mode

## Fallback Chain

1. Load colleague profile from `team-profiles/`
2. Check recent Teams/email history for JP-specific context
3. Use `astemo-colleague-attunement` for general framework
4. If unsure about cultural appropriateness: default to formal Japanese business tone, flag uncertain areas
5. If colleague's current project assignment is unknown: ask operator rather than guessing

## Prerequisites

- Chrome "Sabarish" profile with Teams/Outlook authentication
- Knowledge of colleague's role, department, and JP/US reporting line
- `astemo-colleague-attunement` skill loaded for tone framework
- Awareness of JP business communication norms (senpai/kouhai, honorific levels, nemawashi)

## Error Handling

- **Cultural misstep risk**: If message topic involves escalation, disagreement, or deadline pressure, flag for operator cultural review before finalizing
- **Honorific uncertainty**: When unsure of appropriate formality level, default to higher formality (desu/masu in JP, formal English)
- **Translation drift**: If bilingual adaptation changes meaning, show both versions and flag the divergence
- **Missing context**: If colleague's current project assignment is unknown, ask operator rather than guessing

## Contract

- **NEVER click Send** — always draft → operator reviews → operator sends
- **Preserve Outlook signatures** — do not strip or replace existing signature blocks
- **No invented information** — do not fabricate details about the colleague's schedule, decisions, or project status
- **No agent system references** — keep internal SHADOW/agent/tool references out of message content
- **Bilingual boundary** — English is default; add Japanese only when operator explicitly requests or when the colleague's messages are in Japanese. Never mix unannounced.
- **JP-originated content immunity** — do not rename or restructure files/messages originating from JP colleagues
- **Operator correction override** — if operator corrects a detail about the colleague, use the corrected version immediately for the rest of the session
