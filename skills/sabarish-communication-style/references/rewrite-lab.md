# Rewrite Lab

Use this when a draft feels wrong but the issue is not obvious.

## Anti-pattern: agent overhelping

### Bad

```text
Maybe the better question for IT is what path they want engineering to use:

1. M365 Copilot only
2. GitHub Copilot / GitHub Copilot CLI
3. Azure / Graph API access
4. Enterprise tools like Claude or OpenAI, if approved
```

### Why it fails

- Too much structure for a light thread.
- Sounds like a policy memo.
- Makes the sender look like they are steering a broad IT strategy discussion.

### Better

```text
The open question may be what approved path IT wants engineering to use for these cases: M365 Copilot only, GitHub Copilot / Copilot CLI, Azure / Graph API access, or another approved enterprise tool.
```

## Anti-pattern: tool-first framing

### Bad

```text
Claude is much better than Copilot for this type of work.
```

### Better

```text
The engineering workflow is different from Office summarization. We may need support for scripts, local files, Git workflows, and repeatable automation.
```

## Anti-pattern: sounding like escalation too early

### Bad

```text
These requests have been open for several weeks and are still not fully resolved.
```

### Better

```text
Those requests are still open, so it may help to confirm which approved path IT wants us to use.
```

## Anti-pattern: duplicate close

### Bad when Outlook signature is present

```text
Thanks,
Sabarish

Regards,
Sabarish Duvvuru
...
```

### Better

End on the action sentence:

```text
I can pull together the current request status and a short comparison if that helps.
```

## Anti-pattern: greeting mismatch

### Bad

```text
Hi Bryan, Yingying,
```

Not wrong, but can look clipped or awkward in a formal email.

### Better

```text
Hi Bryan and Yingying,
```

## Anti-pattern: too much background first

### Bad

```text
As you know, the team has been discussing different AI options and IT approval paths for a while, and there are several different tools that may be relevant depending on the use case.
```

### Better

```text
One thing I would separate is the type of AI support we are talking about.
```

## Anti-pattern: weak verbs

| Weak | Better |
|---|---|
| make a clarification | clarify |
| conduct a review | review |
| provide support for | support |
| perform an analysis | analyze |
| have a discussion | discuss |
| make a decision | decide |
| give approval | approve |
| be responsible for management of | manage |

## Anti-pattern: overly polished corporate tone

### Bad

```text
This would enable us to optimize engineering productivity while ensuring alignment with enterprise compliance expectations.
```

### Better

```text
This would help engineering work faster while staying inside the approved IT path.
```

## Anti-pattern: too vague to act

### Bad

```text
Let me know your thoughts.
```

### Better

```text
Does this direction look OK, or should I hold until IT confirms the approved path?
```

## AI smell checklist

Remove or rewrite if the draft has:

- too many balanced clauses
- every paragraph starts with a transition word
- more than one abstract noun per sentence
- perfect symmetry, like 4 bullets with equal length
- `critical`, `robust`, `seamless`, `comprehensive`, `optimize`, `leverage`
- a conclusion paragraph that restates the whole message
- unnecessary `I hope`, `I wanted to`, `I would like to`

## Sabarish realism checklist

A Sabarish draft should feel like:

- a busy engineer wrote it between meetings
- the facts are right
- the ask is practical
- the tone is respectful
- there is no theater
- it could be sent from Outlook without looking generated
