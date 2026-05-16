---
name: quality-form-workflows
description: Use for quality systems quality-form workflows covering DFMEA, DRBFM, quality schedule, and related form content generation or review.
---

# quality-form-workflows

Use when the work is inside the quality systems form itself, not just identifying which standard applies.

## When to Use

- DFMEA work
- DRBFM work
- quality schedule / FormB work
- RPN or failure-mode analysis

## Workflow

1. Identify which form lane the task belongs to:
   - DFMEA (`design-fmea`)
   - DRBFM (`change-risk-review`)
   - quality schedule / FormB (`quality-schedule-form`)
2. Extract the change or concern driving the form update.
3. Write or review the content in the form's native logic:
   - failure mode logic for DFMEA
   - changed -> concern -> countermeasure logic for DRBFM
   - milestone/activity logic for FormB
4. Check consistency with tracker, drawing, and project state.

## Key Decisions

- **DFMEA**: reason in terms of failure mode, effect, cause, controls, and action.
- **DRBFM**: reason from the change point outward.
- **FormB**: reason from project activity and gate ownership, not design detail.

## Gotchas

- Reusing DFMEA logic when the task is really DRBFM.
- Forgetting that FormB is a schedule/ownership artifact, not a technical risk register.
- Writing form content without reconciling the underlying change source first.

## Crystallized From

- Session: 2026-04-17 on JARVIS
- Request: crystallize from all quality systems

