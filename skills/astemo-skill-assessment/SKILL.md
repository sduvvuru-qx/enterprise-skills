---
name: Astemo Skill Assessment
description: 'Use when the user needs: Assess and update Astemo department skill-assessment Excel workbooks from Outlook attachments. Use when Kevin Butler or Astemo asks to update individual skill ratings, development plan ratings, or a workbook with 2024/2025/2026 skill columns.'
icon: icon.svg
metadata:
  filePattern: '**/*Skill Assessment*.xlsx'
  bashPattern:
  - Skill Assessment
  - skill assessment
  - Development Plan
  - Kevin Butler
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-skill-assessment
  icon_style: craft-category-glyph-v1
---

# Astemo Skill Assessment

## Purpose

Use this skill to safely inspect, interview for, and update Astemo skill-assessment workbooks. The default is conservative: only update the current-year rating column after user self-assessment, and never edit email or send responses without explicit user approval.

## Inputs

- Outlook email or downloaded workbook attachment.
- Workbook with columns like `Section`, `Skill`, prior years, and current blank year.
- User self-assessment answers gathered through the interview workflow.

## Workflow

1. Open the Outlook email in the Astemo Chrome profile and identify the attachment, sender, due date, and requested action.
2. Download or open the workbook, then inspect it read-only first.
3. Confirm the sheet name, sections, skill rows, prior ratings, and current-year blank cells.
4. Interview the user by section, using the rating legend in the workbook.
5. Convert answers into a row-to-rating map for the current-year column only.
6. Run `scripts/update_skill_assessment.py` with the workbook path and ratings JSON.
7. Validate that only the intended current-year cells changed.
8. Leave the updated workbook local unless the user explicitly approves upload, reply, or send.

## Rating Scale

- `1`: Basic familiarity with the job or supplier process.
- `2`: Can perform with help or has basic process knowledge.
- `3`: Can perform all job elements solo or is proficient.
- `4`: Can train others to perform, as determined by supervisor.

## Conservative Defaults

- Preserve prior-year ratings exactly.
- Preserve workbook formatting, merged cells, sheet names, and formulas.
- Do not infer inflated ratings; use prior-year value as the baseline and ask for evidence before moving up.
- Blank current-year cells may remain blank if the user says the skill is not applicable or there is not enough evidence.
- Save an updated copy with `_updated` in the filename unless the user explicitly asks to overwrite.

## Helper

```bash
python3 /Users/sdluffy/.agents/skills/astemo-skill-assessment/scripts/update_skill_assessment.py \
  --workbook "$HOME/Downloads/Skill Assessment_SD_202510.xlsx" \
  --ratings ratings.json
```

The ratings JSON accepts either row numbers or exact skill names:

```json
{
  "year": 2026,
  "ratings": {
    "SASG Process framework": 3,
    "GF Engineering:  Laserfiche": 4,
    "41": 3
  }
}
```
