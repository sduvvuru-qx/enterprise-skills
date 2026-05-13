# Copilot Pages DOCX/PDF Export Reverse-Engineering Spec

Date: 2026-05-07
Scope: Microsoft 365 Copilot Pages / Loop-backed page export behavior for an Astemo adaptive artifact canvas.

## Executive conclusion

Copilot Pages is best treated as a **semantic Loop page authoring surface**, not as a controllable document-layout engine.

- **PDF path:** `Create → PDF` opens a Loop print renderer route. The route contains a base64url JSON payload pointing back to the SharePoint-hosted `.page` file. The renderer loads the Loop page through SharePoint/Fluid APIs and hands off to the browser print/PDF pipeline. This gives good structural PDF output, but not deterministic Astemo-grade visual control.
- **DOCX path:** Official behavior is `Create → Document`, Copilot prepares a draft, then exposes **Open Word**. The output opens in Word for the web. In this session the Document click did not produce a visible follow-on flow or captured network/window-open event, so the exact DOCX generation service endpoint remains unverified.
- **Deepest practical control:** Use native semantic blocks: heading hierarchy, tables, checklists, separators, and code blocks. For exact Astemo styling, use a downstream DOCX/PDF/PPTX renderer outside Copilot Pages.

## Observed PDF export pipeline

### UI action

Inside the Copilot Page side panel:

```text
Create → PDF
```

### Captured browser behavior

A `window.open()` call was triggered with this route shape:

```text
https://loop.cloud.microsoft/print/<base64url-json>?app=copilot
```

The decoded payload shape was:

```json
{
  "p": {
    "u": "https://<tenant>.sharepoint.com/:fl:/r/contentstorage/<partition>/Document%20Library/Copilot/<page-name>.page?..."
  }
}
```

This means the PDF command does not expose a simple `GET /export.pdf` endpoint. It opens a Loop print page that points back to the canonical SharePoint `.page` object.

### Print renderer network calls

When the print route loaded, it pulled the page through Loop/Fluid/SharePoint surfaces, including these endpoint shapes:

```text
GET  https://ecs.office.com/config/v1/Fluid/0.0.0.1?...hostName=Loop+App
GET  https://res.cdn.office.net/fluid/prod/loop-app/hashed/LoopPageContainer.<hash>.js
GET  https://graph.microsoft.com/v1.0/me/informationProtection/sensitivityPolicySettings?...file
GET  https://graph.microsoft.com/v1.0/me/informationProtection/sensitivityLabels?...file
GET  https://<tenant>.sharepoint.com/contentstorage/<partition>/_api/v2.1/drives/<drive-id>/items/<item-id>/opStream/capabilities?$select=canWrite,canPrint,canExtract,canExport,supportsCLP
GET  https://<tenant>.sharepoint.com/contentstorage/<partition>/_api/v2.0/drives/<drive-id>/items/<item-id>?select=webUrl,webDavUrl,sharepointIds
POST https://<tenant>.sharepoint.com/_api/v2.1/drives/<drive-id>/items/<item-id>/opStream?ump=1&filter=sequenceNumber...
POST https://<tenant>.sharepoint.com/_api/v2.1/drives/<drive-id>/items/<item-id>/opStream/joinSession?ump=1
POST https://<tenant>.sharepoint.com/_api/v2.1/drives/<drive-id>/items/<item-id>/opStream/snapshots/trees/latest?ump=1&mds=500000000
POST https://<tenant>.sharepoint.com/_api/v2.0/drives/<drive-id>/items/<item-id>/oneDrive.extractSensitivityLabels
```

### Renderer behavior

The print route title became `PDF`, then resolved to the page title. Accessibility snapshot exposed rendered Loop table/header text from the canvas. Attempting screenshot after load destabilized the automation target, consistent with browser/native print handling.

Official Loop documentation also describes PDF export as browser print behavior: open **Print & PDF export**, then choose **Save as PDF** in the browser print dialog.

## Observed DOCX path

### Official behavior

Microsoft support documentation says:

1. Select **Create**.
2. Select **Document**.
3. Copilot prepares the draft.
4. When ready, select **Open Word**.
5. The page opens in Word for the web.

### Session result

In this session, `Create → Document` did not produce a visible document, popup, or captured `fetch`/`XHR`/`window.open` event during the observed window. Therefore:

- The **UI entry point is verified**.
- The **official Word workflow is verified by Microsoft docs**.
- The **actual DOCX generation endpoint is not verified** from live network evidence.

Likely runtime class: asynchronous Copilot/Loop-to-Word conversion that creates or opens a Word document in SharePoint/OneDrive and then opens it in Word for the web. Further verification requires a successful Document run and capture of the resulting `word.cloud.microsoft` / WOPI / SharePoint `.docx` creation calls.

## Deepest controllable export spec inside Copilot Pages

Copilot Pages can reliably control **semantic structure**, not pixel layout.

### Strong controls

| Control layer | PDF | DOCX | Notes |
|---|---:|---:|---|
| Heading hierarchy | High | High | Use one H1, then H2/H3 sections. |
| Paragraph order | High | High | Keep sections linear. |
| Tables | High | High | Best native representation for Astemo business artifacts. |
| Checklists | Medium/High | Medium/High | Works for task/action gates; verify after export. |
| Code blocks | Medium | Medium | Good for JSON/Markdown payloads; may become preformatted text. |
| Separators | Medium | Medium | Useful for print/PDF section breaks, not true page breaks. |
| Link text | Medium | Medium | Export behavior depends on renderer. |
| Evidence/owner/due fields | High | High | Keep as explicit table columns. |

### Weak or unavailable controls

| Control | Status | Reason |
|---|---|---|
| HTML/CSS | Not available in Pages | Copilot Pages renders native blocks, not arbitrary HTML. |
| Exact fonts | Not controllable | Loop/Word/browser controls fonts. |
| Exact Astemo red accents | Not controllable in Pages | No CSS/style layer. |
| Logo placement | Not native | Requires image insertion or downstream renderer. |
| PPTX master layout | Not native | Requires PowerPoint/renderer. |
| Deterministic page breaks | Weak | Browser print and Word conversion decide pagination. |
| PDF metadata/security | Weak | Browser/tenant policy controls PDF output. |
| DOCX style IDs/theme | Weak | Word conversion controls styles unless post-processed. |

## Recommended artifact grammar for maximum export quality

Use this shape inside Copilot Pages:

```markdown
# <Artifact Title>

---

| Field | Value |
|---|---|
| Audience | <Audience> |
| Program | <Program or Needs confirmation> |
| Owner | <Owner or Needs confirmation> |
| Status | <Draft / Approved / Needs confirmation> |
| Date | <YYYY-MM-DD> |
| Classification | <Internal / Customer-ready / Needs confirmation> |

## Executive Summary
<3-5 concise lines, evidence-based.>

## Evidence
| Fact | Source | Confidence | Notes |
|---|---|---|---|

## Decisions
| Decision | Basis | Owner | Status |
|---|---|---|---|

## Risks
| Risk | Impact | Mitigation | Owner |
|---|---|---|---|

## Actions
| Priority | Action | Owner | Due | Status |
|---|---|---|---|---|

## Export Payloads

### PDF-ready Markdown
```text
<clean markdown payload>
```

### PPTX Slide JSON
```json
[
  { "slide": 1, "title": "...", "type": "title", "content": { } }
]
```
```

## Astemo-specific export rules

| Rule | Implementation inside Pages | Why |
|---|---|---|
| Table-first | Use metadata/evidence/decision/risk/action tables | Best survives PDF and Word conversion. |
| White-field discipline | Avoid decorative content | Loop/Word default already uses light surfaces. |
| Red as structural accent only | Encode as labels like `Status: High`; apply color downstream | Pages cannot guarantee `#B6001A`. |
| Unknowns explicit | Use `Needs confirmation` | Preserves engineering correctness. |
| Audience safety | Keep internal runtime notes out of visible artifact | Prevents hidden implementation leakage. |
| Export payload separation | Put JSON/Markdown in code blocks | Keeps machine-readable sections copyable. |

## Downstream renderer spec for true fidelity

For Astemo-grade output, keep Copilot Pages as authoring + logic, then run deterministic renderers.

### DOCX renderer target

Use OOXML / WordprocessingML or `python-docx`.

Minimum controls:

- `document.xml`: paragraphs, heading levels, tables, list/checklist rows.
- `styles.xml`: Normal, Heading 1/2/3, table style, code block style.
- `settings.xml`: compatibility and proofing defaults.
- `sectPr`: page size, margins, header/footer if needed.
- `theme/theme1.xml`: optional theme colors.
- Table borders: use Astemo red `#B6001A` only for top border or section accent.
- Font fallback: Arial if available; otherwise Calibri/Segoe fallback.

### PDF renderer target

Preferred deterministic pipeline:

```text
Copilot Page semantic payload → normalized JSON/Markdown → HTML/CSS print template → PDF
```

Minimum controls:

- Page size: Letter/A4 selectable.
- Margins: 0.5–0.7 in.
- Font: Arial/Segoe fallback.
- Accent: `#B6001A` top rules / status bands only.
- Tables: fixed layout, repeat header rows if renderer supports it.
- Page breaks: explicit `break-before`/`break-inside: avoid` in downstream HTML/CSS.
- Header/footer: artifact title, date, classification, page number.

### PPTX renderer target

Use the existing `pptx_slide_schema` from the page and map it with `python-pptx`:

- 16:9 widescreen.
- Title band or top accent line in `#B6001A`.
- White background.
- Arial/Segoe text.
- Table-first slides.
- Footer: program, date, classification.

## Verification status

| Claim | Evidence | Level | Residual risk |
|---|---|---|---|
| PDF uses Loop print route | Captured `window.open()` to `loop.cloud.microsoft/print/<payload>?app=copilot`; decoded payload points to SharePoint `.page` | High | Exact post-render browser print dialog behavior not screenshot-verified due target instability. |
| Print renderer loads Loop/SharePoint Fluid data | Network calls to Loop App JS, SharePoint opStream, snapshots, capabilities, labels | High | Response bodies not captured. |
| Copilot Pages PDF export is structurally print/PDF pipeline | Official Loop doc + observed print route | High | Copilot support wording says “generate PDF,” but implementation observed is print-route based. |
| DOCX UI path exists | Create menu contained Document; official support doc confirms Open Word flow | Medium | Live endpoint not captured because Document click produced no visible flow. |
| Exact Astemo visual fidelity cannot be guaranteed in Pages | Confirmed native Pages/Markdown-like rendering and no HTML/CSS rendering | High | Microsoft may add richer rendering later. |

## Next best probe

To fully reverse engineer DOCX:

1. Restore/reopen browser session.
2. Trigger `Create → Document` on a simpler page with minimal content.
3. Capture network filtered for:
   - `word.cloud.microsoft`
   - `word-edit.officeapps.live.com`
   - `WopiFrame.aspx`
   - `.docx`
   - `create`, `convert`, `export`, `draft`, `Open Word`
   - SharePoint item creation under `Document Library/Copilot/`
4. Record the new `.docx` item ID, Word URL, and any conversion service call.
5. Compare generated DOCX ZIP contents against semantic source page.
