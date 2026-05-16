---
name: shadow-ent-surface-routing
description: Use to choose the right enterprise surface across Outlook, Teams, SharePoint, OneDrive, Smartsheet, and local artifacts.
---

# shadow-ent-surface-routing

Use when the task is blocked by enterprise source-system ambiguity.

## When to Use

- "where do I find this"
- "which system has the source of truth"
- work spans more than one enterprise surface

## Routing Heuristic

- Outlook: formal requests, approvals, attachments
- Teams: follow-up chat, quick asks, channel recaps
- SharePoint: canonical shared docs and controlled artifacts
- OneDrive: personal/shared working files
- Smartsheet: schedules, owner/date/status grids
- Local files: exports, staged evidence, working copies

## Workflow

1. Identify the question type:
   - decision / approval
   - discussion / recap
   - document / artifact
   - tracker / status table
2. Route to the most authoritative system first.
3. Pull a second surface only if the first one is incomplete or stale.
4. Keep the answer tagged to the system each claim came from.

## Key Decisions

- **Document question**: SharePoint before copied local files.
- **Task/owner/date question**: Smartsheet or tracker before chat fragments.
- **"What happened?"**: meeting artifact first, then Teams/Outlook.

## Gotchas

- Treating local exports as canonical after the live system changed.
- Reading chat fragments before the actual reviewed document.
- Losing source attribution when combining multiple systems.
