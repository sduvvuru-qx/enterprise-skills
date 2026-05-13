# Advanced Communication Research

Use this when drafting requires more than basic email hygiene: leadership sensitivity, JP-US context, async collaboration, or wide-thread risk.

## Research base

This synthesis draws from:

- Google Technical Writing: audience analysis, active voice, clear sentences, short sentences, lists/tables.
- Digital.gov / PlainLanguage: active voice, present tense, hidden verbs, audience-specific clarity.
- GOV.UK content design and style: user need, plain English, front-loading, concise specialist writing, bullet discipline.
- Nielsen Norman Group: skimming behavior, succinct writing, inverted-pyramid principle.
- Purdue OWL: email etiquette, thread hygiene, recipient checking, concise paragraphs.
- Grammarly professional email guidance: task-focused structure, clear subject/greeting/body/call-to-action, recipient review.
- Atlassian Team Playbook: working agreements, async vs sync norms, communication channel purpose, escalation process clarity.
- Japan-guide: Japanese name order and honorifics, `san` as neutral common title.
- Cultural Atlas Japan: indirect communication, face-saving, silence, respectful speech, sensitivity to hierarchy and status cues.

## 1. Audience is not generic

Google frames good technical writing as the difference between what the audience needs to do and what they already know. For Sabarish, translate that into email:

```text
Good message = what this recipient needs to act - what this recipient already knows from the thread
```

Practical rule:

- Bryan does not need raw implementation detail unless it changes a decision.
- Kevin needs blocker/owner/recommendation.
- Yingying needs support and practical alignment.
- JP counterparts need purpose, exact ask, and respectful routing.
- External audiences need confirmed/pending/next step, not internal reasoning.

## 2. Front-load the useful point

Readers skim. Put the point early.

Bad:

```text
After looking into the different AI tools and IT requests, I think there are a few different items that may need clarification.
```

Better:

```text
The open question is which approved path IT wants engineering to use.
```

Use the first 2 lines for:

- purpose
- bottom line
- ask
- risk

Do not spend the first paragraph warming up unless relationship or sensitivity requires it.

## 3. Active voice clarifies ownership

Passive voice hides owner and can create risk in engineering/program emails.

Bad:

```text
The request is still being reviewed.
```

Better if known:

```text
IT is still reviewing the request.
```

Better if owner is not known:

```text
The request is still open, and I can confirm the current owner before we follow up.
```

Do not invent owners to satisfy active voice. If owner is unknown, say so.

## 4. Use lists only when they earn their space

GOV.UK and Google both support lists for readability, but list misuse can make a light thread feel like a memo.

Use bullets for:

- action owners
- options
- confirmed vs pending
- decision criteria

Avoid bullets for:

- a casual alignment reply
- a single distinction
- a thread where the previous message was short

For 3+ options in a light reply, prefer a compact sentence.

## 5. Async collaboration needs explicit next step

Atlassian working-agreement guidance emphasizes shared communication norms, channel purpose, and escalation path. For email, this means every async message should reduce ambiguity:

- Who is expected to respond?
- What should they decide or confirm?
- By when, if time matters?
- What happens if nobody responds?

Useful endings:

```text
I can pull this together if helpful.
```

```text
Does this direction look OK, or should I hold until IT confirms the approved path?
```

```text
I will update the tracker after JP confirms the drawing status.
```

## 6. JP-US communication requires hierarchy and translation awareness

Japan-guide notes `san` is neutral and usable in most situations. Cultural Atlas notes Japanese communication often values indirectness, harmony, face-saving, respectful speech, and status awareness.

For Astemo JP communication:

- use `-san`
- avoid idioms
- avoid direct blame
- use purpose + current status + exact ask
- preserve seniority in greeting and recipient order when practical
- preserve original thread order if seniority is unclear
- ask user before reordering sensitive JP recipient lists

Bad:

```text
Hayakawa, can you check why this is late?
```

Better:

```text
Hayakawa-san,

Quick confirmation for the FH4S drawing status.

Could you confirm the current owner and expected timing? I want to keep the US-side tracker aligned before sharing the next update.
```

## 7. Seniority is part of the message

In JP-heavy or mixed JP-US threads, name order can communicate respect.

Apply seniority/order to:

- To field
- Cc field
- greeting
- action tables
- reviewer lists

If unsure, use original thread order. Do not reorder casually.

## 8. Forward-safe writing

Grammarly and Purdue both note that professional email can be forwarded or shared. For Astemo, assume any message could reach Bryan, Kevin, IT, JP leadership, or a customer.

Forward-safe wording avoids:

- blame
- speculation
- unsupported claims
- sensitive internal friction
- tool advocacy without business need

Use:

```text
The open item appears to be IT approval/routing.
```

Not:

```text
IT has been sitting on this for weeks.
```

## 9. Tone ladder

Use the least formal tone that is still safe.

| Context | Tone |
|---|---|
| peer Teams | short, direct, friendly |
| peer email thread | natural, clear, light |
| manager email | concise, structured, recommendation-led |
| director email | business impact, decision path, support ask |
| JP counterpart | respectful, precise, seniority-aware |
| customer/supplier | formal enough, factual, no internal noise |

## 10. Sabarish-specific synthesis

The target style is not generic professional email. It is:

```text
engineering practical + manager-readable + human + low-theater
```

A good Sabarish email:

- sounds like a real engineer wrote it
- uses facts without overexplaining
- avoids AI polish
- leaves a clear next step
- respects hierarchy and thread context
- is safe if forwarded

## 11. Research-to-rule mapping

| Research principle | Sabarish rule |
|---|---|
| audience analysis | choose Bryan/Kevin/peer/JP/customer mode before drafting |
| active voice | identify owner when known; do not hide accountability |
| front-loading | put purpose or bottom line in first 2 lines |
| skimmability | short paragraphs, bullets only when useful |
| thread hygiene | reply in original thread, preserve audience |
| recipient review | check To/Cc, reply all, JP seniority order |
| intercultural sensitivity | `-san`, no idioms, respectful exact asks |
| async norms | every message needs next step or owner |
| forward-safe email | no blame, no speculation, no sensitive over-share |
