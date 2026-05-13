# Sabarish Communication Playbook

Use this when a draft needs judgment, not just wording.

## 1. Message diagnosis

Before writing, classify the message on four axes.

### Axis A: What is the recipient supposed to do?

| Goal | Best format | Ending |
|---|---|---|
| FYI | 2 to 4 sentences | `No action needed from your side.` if true |
| Confirm | state assumption + ask | `Can you confirm this direction?` |
| Decide | options + recommendation | `My recommendation is Option [#].` |
| Escalate | bottom line + impact + ask | `Support needed: [specific ask] by [date].` |
| Correct | acknowledge + corrected fact | `I will update the tracker/deck accordingly.` |
| Close loop | result + next owner | `I will keep this moving unless direction changes.` |

### Axis B: How much context do they need?

| Audience | Context level |
|---|---|
| Bryan / director | business impact, policy path, decision/support needed |
| Kevin / manager | execution status, blocker, owner, recommendation |
| Peer engineer | technical detail enough to act |
| JP counterpart | purpose, intended audience, exact confirmation needed |
| Customer / supplier | confirmed facts, pending items, next step, no internal noise |

### Axis C: Is the thread light or formal?

| Thread type | Rule |
|---|---|
| casual reply | no headings, no tables, no heavy lists |
| status update | bottom line + bullets |
| escalation | clear owner/impact/date |
| customer answer | factual, defensible, separates confirmed/pending |
| meeting follow-up | decisions, actions, owners, due dates |

### Axis D: What is the risk?

| Risk | Guardrail |
|---|---|
| confidential RFQ/program data | mention guidelines, do not include sensitive details unnecessarily |
| policy/IT | ask for approved path, do not argue tools as preference |
| schedule gate | include date and impact |
| ownership ambiguity | label DE/QA/SQ/BUP/PMO/IT ownership |
| wide CC | reduce tone, avoid speculation, write forward-safe |

## 2. The 5-line default

Most work messages should fit this unless the user asks for detail:

```text
Hi [name],

[Why I am replying / context.]

[Bottom line or clarification.]

[Specific next step or offer.]
```

If more detail is needed, add one short paragraph or 2 to 3 bullets.

## 3. The leadership rule

For Bryan/Kevin, never make them infer the point.

Bad:

```text
There are a few things going on with the IT requests and Copilot may or may not work depending on the use case.
```

Better:

```text
Bottom line: M365 Copilot covers Office-style work, but it may not cover engineering automation. IT may need to confirm whether GitHub Copilot CLI is the approved path for that use case.
```

## 4. The thread-weight rule

Do not exceed the thread's weight.

If the previous email is 6 sentences, do not respond with 8 paragraphs unless there is a real escalation.

Use this scale:

| Incoming message | Reply weight |
|---|---|
| quick note | 2 to 5 sentences |
| question with 2 asks | answer each ask, max 2 short sections |
| Bryan/Kevin guidance | agree + clarify + next step |
| technical request | answer + evidence + owner |
| customer request | formal answer + confirmed/pending + next step |

## 5. The “forward-safe” test

Assume any email can be forwarded to:

- Bryan
- Kevin
- IT
- JP leadership
- customer/supplier

Remove or reword anything that would sound like:

- blame
- tool advocacy without business reason
- sensitive internal friction
- uncertainty presented as fact
- speculation about another department

Use:

```text
The open item appears to be [owner/action].
```

Instead of:

```text
IT has not resolved this.
```

## 6. Specific rewrite moves

### Move 1: Lead with the answer

Before:

```text
After looking at the different requests, I think there are several things that need to be considered.
```

After:

```text
The requests cover different needs, so I would not treat them as one general AI tool request.
```

### Move 2: Convert tool preference to workflow need

Before:

```text
Claude is better for coding.
```

After:

```text
The engineering need is terminal/script/local-file workflow support, not just Office summarization.
```

### Move 3: Convert complaint to path question

Before:

```text
The requests have been open for weeks.
```

After:

```text
The requests are still open, so it may help to confirm which approved path IT wants us to use.
```

### Move 4: Remove self-conscious phrasing

Before:

```text
I just wanted to add my thoughts here.
```

After:

```text
Adding my thoughts here.
```

### Move 5: Replace heavy list with compact sentence

Before:

```text
The options are:
1. A
2. B
3. C
```

After:

```text
The open question may be whether the approved path is A, B, or C.
```

## 7. Email vs Teams differences

### Outlook email

- More complete context.
- Safer wording.
- Signature handles identity.
- Avoid rapid-fire corrections.
- Reply in thread.

### Teams

- Shorter.
- More direct.
- One ask per message.
- Good for `quick check`, `do you want me to proceed`, `FYI`.

Teams pattern:

```text
Bryan, quick check on [topic].

Bottom line: [bottom line].

Do you want me to [action], or hold until IT confirms the path?
```

## 8. Final review questions

Ask before handoff:

1. Did I answer the actual thread?
2. Did I preserve the right audience?
3. Is the first useful sentence within the first 2 lines?
4. Did I avoid duplicate signature?
5. Would Sabarish actually write this?
6. Would Bryan/Kevin know what to do next?
7. Is anything overexplained because the agent wanted to be helpful?
