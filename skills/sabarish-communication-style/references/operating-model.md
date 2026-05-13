# Communication Operating Model

Use this as the full drafting pipeline when quality matters.

## Pipeline

### 1. Intake

Capture only what changes the draft:

- Thread summary: what was asked, by whom, and why now.
- Desired outcome: inform, ask, align, decide, escalate, close loop.
- Audience: active recipient(s), CC watchers, possible forward audience.
- Constraints: no em dashes, signature present, English only, internal/customer, confidential data.
- Evidence: facts, dates, ticket numbers, request IDs, open/closed state.

### 2. Intent compression

Write the message intent in one sentence before drafting.

Examples:

- `Clarify that M365 Copilot is not the same as engineering automation support, while staying aligned with Bryan's IT guidance.`
- `Ask Hayakawa-san to confirm routing before Sabarish follows up with JP DE.`
- `Tell Kevin what is blocked, who owns it, and the recommended next action.`

If the intent sentence is unclear, do not draft yet.

### 3. Audience calibration

Choose one audience mode:

| Mode | Use for | Tone |
|---|---|---|
| Peer alignment | Yingying, internal engineer | supportive, practical |
| Manager triage | Kevin | status + blocker + recommendation |
| Director visibility | Bryan | bottom line + decision path |
| JP coordination | Hayakawa-san, Hosoya-san | precise, respectful, routing-aware |
| Customer/supplier | Honda, supplier | factual, defensible, no internal noise |

### 4. Shape selection

Pick one shape only.

| Shape | Max length | Use when |
|---|---:|---|
| Micro reply | 2 to 4 sentences | light thread, simple confirmation |
| Clarifying reply | 4 to 7 sentences | one important distinction |
| Action note | bullets | owners/actions/dates matter |
| Escalation note | 3 sections | risk/support/decision needed |
| Customer answer | confirmed/pending/next step | external record |

### 5. Draft

Draft in plain text. Avoid sign-off if Outlook signature is present.

### 6. Humanization

Make it less perfect:

- remove extra transitions
- remove symmetric lists unless needed
- shorten the first paragraph
- replace abstract nouns with concrete nouns
- keep one mild human phrase if appropriate: `I think`, `may be`, `if helpful`; avoid `my read` / `my take` in Astemo-facing drafts

### 7. Safety and forward-safe review

Check:

- Would this be OK if forwarded to Bryan/Kevin/IT/customer?
- Does it blame anyone?
- Does it reveal sensitive details not needed for action?
- Are pending items clearly pending?
- Is there a concrete next step?

### 8. Outlook handoff

Use `outlook-draft-guardrails` for placement, signature, recipients, formatting, and manual-send handoff.

## Scoring rubric

Score each draft 1 to 5.

| Dimension | 1 | 3 | 5 |
|---|---|---|---|
| Thread fit | wrong thread/weight | mostly relevant | exactly matches thread and audience |
| Clarity | buried point | understandable | first 2 lines reveal purpose |
| Actionability | no ask | soft ask | clear next step/owner |
| Human voice | AI/corporate | neutral | sounds like Sabarish |
| Risk control | speculative/sensitive | mostly safe | forward-safe and evidence-backed |
| Brevity | bloated | acceptable | shortest useful version |

Target: 4+ on all dimensions before putting into Outlook.

## Rewrite levels

Use the smallest rewrite that fixes the problem.

1. **Clean**: grammar, spacing, typo, no meaning change.
2. **Tighten**: remove filler and duplicate points.
3. **Humanize**: make less AI-like, more Sabarish.
4. **Reframe**: change from tool-first to workflow/decision-first.
5. **Re-scope**: change audience, thread weight, or ask.

## Stop conditions

Stop and ask user if:

- recipient list is ambiguous
- reply all vs direct reply is unclear
- message could escalate politically
- facts/ticket status are uncertain
- customer-facing commitment is requested
- user asks to send but standing rule says manual send
