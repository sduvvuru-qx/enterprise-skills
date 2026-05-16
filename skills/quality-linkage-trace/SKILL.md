---
name: quality-linkage-trace
description: Use for quality systems linkage and traceability checks across drawing characteristics, DFMEA, control plans, and downstream work instructions.
---

# quality-linkage-trace

Use when the question is whether the quality-document chain still holds together.

## When to Use

- linkage checks
- traceability validation
- document chain reasoning
- "does this drawing requirement flow through the quality docs"

## Workflow

1. Start from the upstream technical requirement:
   - drawing note
   - critical-characteristics characteristic
   - change point
2. Check whether it appears in the next quality artifact:
   - DFMEA
   - control plan
   - work instruction / downstream execution artifact
3. Identify breaks in the chain:
   - missing item
   - inconsistent wording
   - mismatched owner/status
4. Report the exact gap and where the chain breaks.

## Key Decisions

- **Upstream requirement unclear**: do not force a downstream trace result.
- **Partial chain exists**: show the missing hop, not just "pass/fail".
- **Program-specific doc names vary**: preserve the generic chain even when filenames differ.

## Gotchas

- Treating linkage as a general summary instead of a chain check.
- Reporting only the missing downstream item without showing the upstream source.
- Ignoring control-plan/work-instruction steps after DFMEA.

## Crystallized From

- Session: 2026-04-17 on JARVIS
- Request: crystallize from all quality systems
