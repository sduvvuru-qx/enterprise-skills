---
name: shadow-ent-action-followthrough
description: Use for extracting accountable pending work from enterprise meetings, Outlook, Teams, SharePoint, and related engineering follow-up channels.
---

# shadow-ent-action-followthrough

Use when a clean follow-through queue is needed from enterprise context.

## When to Use

- "what do I owe"
- pending items review
- meeting follow-up
- blocker scan before a sync or review

## Workflow

1. Gather the smallest relevant source set.
2. Extract only accountable or clearly relevant items.
3. Normalize to:
   - overdue
   - blocking
   - open
   - waiting
4. Preserve owner, ask, and source for each item.

## Key Decisions

- **No named owner**: do not assign casually.
- **Blocker language present**: elevate above routine tasks.
- **No due date**: preserve urgency wording instead of inventing a date.

## Gotchas

- Turning a recap into a todo list without evidence.
- Pulling in every attendee action instead of the relevant accountable lane.
- Losing blocker relationships.

## Output Shape

- overdue
- blocking
- open
- waiting on others

## Crystallized From

- Session: 2026-04-17 on JARVIS
- Intent: broader enterprise engineering role pack
- Pattern source: reduced from private action-tracker and meeting-followthrough workflows
