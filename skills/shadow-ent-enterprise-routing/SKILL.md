---
name: shadow-ent-enterprise-routing
description: Use to pick the right enterprise source path across Outlook, Teams, SharePoint, Smartsheet, and local files before extracting or summarizing.
---

# shadow-ent-enterprise-routing

Use when the main problem is not analysis yet, but locating the right enterprise source of truth.

## When to Use

- "where do I find this"
- source system ambiguity
- deciding between Outlook, Teams, SharePoint, Smartsheet, or local files
- enterprise context gathering before summary or action extraction

## Routing Heuristic

- **Outlook**: formal asks, approvals, attachments, decisions sent by mail
- **Teams**: chatty follow-through, meeting recap fragments, quick asks, channel context
- **SharePoint**: canonical docs, meeting files, review comments, controlled shared artifacts
- **Smartsheet**: schedule/state tables, activity plans, owner/date tracking
- **Local files**: working copies, exported evidence, in-progress derivatives

## Workflow

1. Identify the question type:
   - decision
   - discussion
   - document
   - schedule / task
2. Route to the most authoritative system first.
3. Pull adjacent systems only if the first source is incomplete.
4. Preserve which system each claim came from.

## Key Decisions

- **Document-based question**: SharePoint before mail excerpts.
- **Owner/date table question**: Smartsheet before free-text messages.
- **"What happened?"**: usually start with calendar/meeting artifact, then Teams/Outlook.

## Gotchas

- Treating copied local files as canonical when the live system changed.
- Reading chat fragments before checking the actual reviewed document.
- Losing source attribution across systems.

## Output Shape

- source chosen
- why it is primary
- supporting systems if needed
- unresolved gaps

## Crystallized From

- Session: 2026-04-17 on JARVIS
- Intent: broader enterprise engineering role pack
- Pattern source: reduced from larger private M365/SharePoint/Smartsheet routing skills
