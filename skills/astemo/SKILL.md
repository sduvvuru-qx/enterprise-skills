---
name: Astemo
description: Use when an Astemo work request is broad, legacy Astemo skills say “prefer Astemo,” or the task needs routing across program context, M365/SharePoint/Outlook/Teams, presentations, spreadsheets, colleague communication, meeting actions, or enterprise artifact boundaries.
icon: icon.svg
metadata:
  depends_on: [astemo-work-context, astemo-calendar, astemo-outlook, astemo-teams, astemo-xlsx, astemo-pipeline, meeting-check, crystallize, skill-surgery-rd]
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo
  icon_style: craft-category-glyph-v1
---

# Astemo

Canonical Astemo entrypoint and router. Use this before resurrecting deprecated `astemo-*` skills.

## Route first

1. Load `astemo-work-context` when program, role, org, task, workflow, filing path, or audience is unclear.
2. Identify the artifact/channel family.
3. Load the narrow active skill for that lane.
4. Keep customer/work artifacts clean: do not expose hidden SHADOW runtime details in Dropbox/M365-visible work products.

## Active lanes

| User intent | Route to |
|---|---|
| Program/context/path/audience | `astemo-work-context`, then program-specific `fh4s-*` only if the program is FH4S |
| M365/SharePoint/Teams/Outlook/Excel | `astemo-hl`, `astemo-sharepoint`, `astemo-teams`, `astemo-outlook`, `astemo-excel-online` |
| Meeting capture/actions/context | `meeting-check`, `astemo-meeting-live-copilot`, `astemo-calendar`, then program skill as needed |
| Presentations/templates/relay packets | `template-surface-router`, `astemo-presentation-prep-relay`, `astemo-org-templates`, `pptx-agent/astemo`, `astemo-template-fallback` |
| Excel/workbooks/trackers | `astemo-xlsx`, `fh4s-change-point-update`, `fh4s-sasg-forms`, `astemo-smartsheet` |
| Colleague communication/profile tone | `astemo-colleague-attunement`, then the named colleague skill if available |
| Dropbox/project schema | `astemo-dropbox`, `astemo-project-schema`, `cloud-drive-routing`, `template-surface-router` |
| DOCX/PDF generation from structured data | `copilot-canvas-renderer`, CLI: `astemo-render payload.json --pdf --open` |
| Pipeline/training/context promotion | `astemo-pipeline`, `crystallize`, `skill-surgery-rd` |

## Legacy rule

If a deprecated skill points here, do not follow its old full workflow. Treat it as a search term, map it to the active lane above, and preserve only uniquely useful gotchas in the active skill/report.

## Crystallized From

- Session: 2026-05-02 Astemo deprecated-skill surgery.
- Pattern: many legacy Astemo skills said “prefer Astemo” before a generic Astemo router existed; this skill closes that routing gap without becoming a giant playbook.
