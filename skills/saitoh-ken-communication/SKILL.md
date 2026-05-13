---
name: Saitoh Ken Communication
description: Use when drafting, replying to, or analyzing Teams/email communication with Ken Saitoh (Saitoh-san) for Astemo program work, especially when adapting tone by role, authority, urgency, and program context.
icon: icon.svg
metadata:
  depends_on: [astemo-colleague-attunement]
  category: enterprise/astemo
  family: enterprise
  lifecycle: active
  canonical_slug: astemo-saitoh-ken-communication
  icon_style: craft-category-glyph-v1
---

# Ken Saitoh Communication

Read profile first:

`/Volumes/☯Duality/spaces/professional/sduvvuru-qx/team-profiles/saitoh-ken.md`

Use `astemo-colleague-attunement` for the general framework.

## Default posture

INV technical lead; inverter-side alignment; JP DE question routing for INV topics

## Best pattern

```text
Saitoh-san — quick INV/IPM boundary check on [topic].

My understanding:
- INV side: [fact]
- IPM side: [fact]

Question for confirmation:
- [specific technical question]

If useful, I can send this as a concise question to JP DE.
```

## Watchouts

- Do not blur INV/IPM ownership.
- Do not ask broad questions; provide the exact technical point.


## Pipeline

```
Intent → Audience analysis → Bilingual tone selection → Draft → Operator review → Operator sends
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `draft` (default) | Teams/email draft for operator review | Operator asks to write/reply |
| `analyze` | Tone/style analysis of received message | Operator shares incoming message |
| `cultural-check` | Cross-cultural appropriateness review | Before sending to JP colleague |

## Artifact Routing

- Draft messages: chat only — never sent automatically
- Meeting notes: `ShadowArchive/80-reports/`

## Prerequisites

- Chrome "Sabarish" profile with Teams/Outlook auth
- `astemo-colleague-attunement` loaded for tone framework

## Contract

- **NEVER click Send** — always draft → operator reviews → operator sends
- No invented information about colleague
- No agent system references in message content

