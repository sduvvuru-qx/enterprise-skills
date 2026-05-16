---
name: quality-standards-map
description: Use to map quality systems IDs, standard families, and topic keywords to the right governing standard and dependency chain.
---

# quality-standards-map

Use when the main problem is identifying which quality systems standard governs the work.

## When to Use

- "which quality systems standard applies"
- K11 / N10 / X10 / N41 questions
- "which form covers this"
- standards lookup before editing a project artifact

## Workflow

1. Identify the subject:
   - quality analysis
   - linkage/traceability
   - transfer risk
   - activity ranking / schedule
2. Map to the likely quality systems family:
   - `K11` = design quality forms such as DFMEA / DRBFM
   - `N10` = production transfer risk
   - `N41` = linkage / quality-document traceability
   - `X10` = activity rank and quality schedule
3. Surface not just the target standard, but the dependency chain around it.
4. Return the standard ID, title, purpose, and why it matters for the task.

## Key Decisions

- **Only keyword, no ID**: map by subject first, then narrow to specific IDs.
- **Multiple standards apply**: return the chain, not a forced single answer.
- **Project-specific phrasing**: separate generic quality systems rule from program-specific application.

## Gotchas

- Treating DFMEA as the whole quality systems universe.
- Naming a form without the dependency chain that actually governs the workflow.
- Collapsing transfer risk, linkage, and activity rank into one generic "quality form" bucket.

## Crystallized From

- Session: 2026-04-17 on JARVIS
- Request: crystallize from all quality systems

