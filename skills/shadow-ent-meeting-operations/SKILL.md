---
name: shadow-ent-meeting-operations
description: Use for enterprise meeting context, action extraction, recording/transcript usage, and related follow-up workflows.
---

# shadow-ent-meeting-operations

Use when the work spans meetings, recaps, transcripts, and follow-through.

## When to Use

- meeting recap
- action extraction
- recording or transcript use
- post-meeting mail/Teams follow-up

## Workflow

1. Anchor on the meeting or thread.
2. Pull adjacent context as needed.
3. Separate decisions, actions, and unresolved issues.
4. Keep recap output different from the action queue.

## Key Decisions

- **Transcript noisy or partial**: cross-check against calendar, Outlook, or Teams before declaring conclusions.
- **Action extraction ask**: return owned tasks separately from the recap.
- **Unknown recording**: identify the meeting before summarizing content.

## Output Shape

- recap
- decisions
- action items
- open questions

## Gotchas

- Turning a recap into an unowned todo list.
- Treating ASR output as verbatim truth.
- Combining discussion, decision, and action into one undifferentiated summary.
