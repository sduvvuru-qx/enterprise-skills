---
name: Astemo Colleague Attunement
description: Use when drafting or analyzing Astemo Teams/email communication for a specific colleague, adapting message style by role, relationship, program, communication history, urgency, and evidence from profiles or prior messages.
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-colleague-attunement
  icon_style: craft-category-glyph-v1
---

# Astemo Colleague Attunement

## Purpose

Use this to tailor Astemo Teams/email drafts to the recipient’s role, working style, program lane, and known communication patterns. Keep it evidence-backed: separate observed facts from inference.

## Required inputs

1. **Recipient** — name, title, or chat/thread identity.
2. **Program/topic** — FH4S/FH4/FH4L/XY9H/etc.; ask once if ambiguous.
3. **Message goal** — FYI, ask, confirm, review, escalate, close loop, request file/access.
4. **Audience/routing** — direct only, CC, JP/US, customer/supplier/internal.
5. **Evidence source** — profile, thread text, meeting recap, local `ASTEMO-CONTEXT.md`, or user-pasted message.

## Profile lookup order

1. Check `team-profiles/<person-slug>.md` in `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/`.
2. Search `ASTEMO-CONTEXT.md` for role/program facts.
3. If requested and approved, inspect Outlook/Teams read-only evidence.
4. If no profile exists, draft conservatively and suggest creating one.

## Attunement frame

For each colleague, decide:

| Dimension | Question |
|---|---|
| Role | Are they manager, lead, peer, JP counterpart, QA/SQA/PE/BUP, supplier/customer? |
| Authority | Are they decision maker, reviewer, routing gate, contributor, or escalation path? |
| Style | Do they prefer options, direct asks, evidence, schedule impact, high-level summary, or detailed backup? |
| Risk | Is the topic sensitive: IP, customer, supplier, schedule gate, personnel, escalation? |
| Relationship | Are you asking, informing, challenging, escalating, or closing a loop? |
| Channel | Teams short-form vs email/thread formal vs deck/tracker artifact. |

## Drafting pattern

For Teams, prefer:

```text
[Name]-san — quick [goal] on [program/topic].

Purpose: [why]
Audience/use: [who/where]

Current status:
- [fact]
- [open item]

Suggested next step:
- [action / option]

[Precise ask]
```

For email, add subject, context, decision/action table, due date, and CC rationale.

## Output requirements

When asked to draft a message:

1. Provide **one recommended draft** first.
2. Add **why this style fits** in 2–4 bullets.
3. If sensitivity exists, include a **safer alternate**.
4. Do not send; ask for approval if sending is requested.

When asked to create/update a profile:

- Use `references/profile-schema.md`.
- Cite evidence source lines/threads where possible.
- Mark unverified inferences as hypotheses.
- Include reusable message templates.

## Coverage Expansion (Cambrian Priority #3)

Current coverage: **~55%** of daily communication workflow automated.
Target: **80%** by end of Q2 2026.

### Quick-Reply Templates (per-colleague)

When the operator says "reply to [name]" and the colleague has a profile:

#### To Bryan Voris (Manager)
```
Bryan —

[Status in 1-2 lines].

[Key risk or decision needed, if any].

[Proposed next step].
```
Style: Direct, bottom-line-first, no padding. Bryan reads fast.

#### To Hayakawa-san (JP DE Lead)
```
Hayakawa-san —

Thank you for [reference]. Here is the US DE update:

[Status by item, numbered].

Could you confirm [specific JP input needed] by [date]?

Best regards,
Sabarish
```
Style: Respectful, numbered items, explicit date requests. JP audience.

#### To Kevin Butler (QA/SQ)
```
Kevin —

[Topic]. Current status:

- [Fact 1]
- [Fact 2]

[Ask or alignment needed].
```
Style: Evidence-first, structured. Kevin wants data, not narrative.

#### To Miki-san (PMO)
```
Miki-san —

Regarding [topic/program]:

[Current status].

[Schedule impact, if any].

[Ask or proposal].
```
Style: Schedule-aware, concise. Miki tracks timelines.

#### To Hosoya-san (Director)
```
Hosoya-san —

[Context]. Summary:

[Key decision or status].

[Recommendation].
```
Style: Executive, brief. Hosoya-san wants the decision point, not the process.

### Reply-in-Thread Reliability Checklist

Before placing a reply draft in an existing thread:

1. ✅ Match the thread's existing tone (formal/informal)
2. ✅ Quote or reference the specific message you're replying to
3. ✅ Preserve the thread's language (JP/EN bilingual threads stay bilingual)
4. ✅ Don't introduce new topics — start a new thread instead
5. ✅ Check recipients: is this reply-to-all or reply-to-sender?
6. ✅ Attachments: reference the attachment name, don't assume they opened it

### Communication Coverage Tracker

Write a per-session communication log:

```json
{
  "date": "2026-05-04",
  "drafts_produced": 3,
  "colleagues": ["bryan-voris", "hayakawa"],
  "types": ["reply", "new-topic", "escalation"],
  "coverage_pct": 55,
  "next_target": "Add Saitoh-san template"
}
```

Save to `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/sessions/comm-log.jsonl`

## Common mistakes

- Do not over-personalize from one message.
- Do not infer title/authority beyond evidence.
- Do not use GLM/Z.AI for PII/EII/private/sensitive enterprise content.
- Do not paste full private threads into broad artifacts; summarize patterns.
- Do not default ambiguous work to FH4S.
