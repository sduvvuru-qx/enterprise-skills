---
name: shadow-ent-quality-systems-operations
description: Use for broad quality-systems standards, forms, linkage, and production-transfer risk workflows across enterprise quality work.
---

# shadow-ent-quality-systems-operations

Use when quality systems logic governs the work, whether the task is standards lookup, form content, traceability, or transfer risk.

## When to Use

- quality systems standard questions
- DFMEA / DRBFM work
- linkage or traceability checks
- transfer-risk or activity-rank reasoning

## Workflow

1. Decide if the task is standards lookup, form execution, linkage, or transfer risk.
2. Map to the governing quality systems family or artifact.
3. Preserve the chain from standard requirement to project artifact.
4. Report gaps explicitly.

## Key Decisions

- **Standards question**: answer with the governing ID and why it applies.
- **Form question**: shift from the standard to the artifact logic and required fields.
- **Linkage question**: show the broken chain, not just "pass/fail".
- **Transfer-risk question**: include activity/schedule implications, not just risk labels.

## Output Shape

- quality systems lane
- governing standard or artifact
- implication for the current task
- open compliance gap if any

## Gotchas

- Treating DFMEA as the whole quality systems scope.
- Naming a form without explaining the upstream standard logic.
- Ignoring linkage or transfer-risk consequences after answering the first question.
