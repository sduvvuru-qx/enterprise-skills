---
name: FH4S Smart Drawing
description: 'Use when extracting structured data from Astemo/Keihin (KISO規格) PDF drawings and create Smart Drawing JSON sidecar files. Handles rasterized CCITT Group 4 PDFs using pikepdf. Covers title block, BOM, notes, KC/VP characteristics, revision history, and geometry. Use for: "smart drawing", "extract drawing", "process PDF drawing", "drawing metadata", "wrap drawing", "KISO template". Trigger on FH4S/XP7G drawing PDFs or when user mentions smart drawings.'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  priority: 85
  filePattern: '**/drawings/**/*.pdf'
  bashPattern: smart.drawing|smart_drawing|pikepdf|kiso
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-smart-drawing
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# FH4S Smart Drawing — Extraction & Wrapping

## Purpose
Transform static rasterized PDF drawings (ex-Keihin/KISO format) into structured, queryable
JSON sidecar files. Each `.smart.json` sits alongside its source PDF and contains extracted
title block, BOM, notes, KC/VP quality characteristics, revision history, and geometry metadata.

## Prerequisites
- Load **fh4s-context** for part numbers, org chart, project scope
- Python venv at `/Volumes/SHADOW/ASTEMO/fh4s/.venv` with `pikepdf`, `pdfplumber`, `Pillow`

## Key Files
| File | Path | Purpose |
|---|---|---|
| KISO Template | `/Volumes/SHADOW/ASTEMO/fh4s/tools/templates/smart-drawing-kiso.template.json` | Schema + field docs for ex-Keihin drawings |
| Extractor Script | `/Volumes/SHADOW/ASTEMO/fh4s/tools/scripts/smart_drawing_extract.py` | pikepdf-based raw image extraction + zone cropping |
| Smart JSONs | `inputs/drawings/**/*.smart.json` | Extracted data, co-located with PDFs |

## Drawing Origins (Astemo = 4 merged companies)
| Origin | Stamp | Template | Projects |
|---|---|---|---|
| **Keihin** | KISO規格適用 (top-left) | `kiso-keihin` | FH4S, XP7G |
| **Hitachi** | (different format) | `hitachi` (TBD) | Other |
| **Showa** | (different format) | `showa` (TBD) | Other |
| **Nissin Kogyo** | (different format) | `nissin` (TBD) | Other |

**Current scope**: Only KISO/Keihin template is implemented.

## PDF Technical Details
Astemo/Keihin drawings exported from CATIA V5 are:
- **Rasterized**: Single embedded image, zero extractable text/vectors
- **CCITT Group 4 fax-encoded**: 1-bit (black/white), `/CCITTFaxDecode` filter
- **Massive**: Up to 78,898 × 13,244 px (~1 billion pixels)
- **Requires**: `PIL.Image.MAX_IMAGE_PIXELS = 1_500_000_000` to avoid decompression bomb errors
- **pdfplumber/PyMuPDF cannot render these** — use `pikepdf.PdfImage.as_pil_image()` directly

## Extraction Workflow

### 1. Extract Raw Image
```python
import pikepdf
from PIL import Image
Image.MAX_IMAGE_PIXELS = 1_500_000_000

pdf = pikepdf.Pdf.open(pdf_path)
obj = pdf.pages[0]['/Resources']['/XObject']['/img0']
pil_img = pikepdf.PdfImage(obj).as_pil_image()
```

### 2. Classify Drawing Type
```python
w, h = pil_img.size
ratio = w / h
drawing_type = "assembly" if ratio > 3.0 else "component"
```

### 3. Crop Zones (KISO Layout)

**Component drawings** (lead frames, single parts):
| Zone | Fractional coords (x0, y0, x1, y1) |
|---|---|
| Title block | (0.78, 0.70, 1.00, 1.00) |
| Notes (EN) | (0.55, 0.75, 0.78, 1.00) |
| Notes (JP) | (0.35, 0.75, 0.55, 1.00) |
| KISO stamp | (0.00, 0.00, 0.08, 0.06) |
| KC/VP count | (0.55, 0.82, 0.70, 0.88) |

**Assembly drawings** (IPM COMP, PM COMP):
| Zone | Fractional coords (x0, y0, x1, y1) |
|---|---|
| Title block | (0.88, 0.70, 1.00, 1.00) |
| BOM table | (0.90, 0.00, 1.00, 0.68) |
| Material list | (0.82, 0.02, 0.92, 0.55) |
| Notes | (0.45, 0.80, 0.88, 1.00) |

### 4. Visual Read Crops
After cropping, use Codex's vision to read each zone and populate the template fields.
The lead frame drawings are readable at native resolution; assembly drawings need the
crop approach due to their massive size.

### 5. Write Smart JSON
Save as `<drawing-number>.smart.json` alongside the PDF. Follow the KISO template schema.

## Smart JSON Schema (Key Fields)

```json
{
  "$schema": "smart-drawing/v1",
  "$template": "kiso-keihin",
  "sourceFile": "<filename>.pdf",
  "titleBlock": { "drawingNumber", "partName", "partNo", "material", "mass", "issuedDate", "personnel" },
  "revisionHistory": [{ "rev", "ecn", "description", "hondaRef" }],
  "standards": { "kisoMark": true, "partsSpec": "BSY9-5306" },
  "qualityCharacteristics": { "kcCount", "vpCount", "kcItems": [], "vpItems": [] },
  "notes": [{ "id", "kcVp", "text" }],
  "bomItems": [{ "itemNo", "partNo", "drawingNo", "name", "qty" }],  // assembly only
  "geometry": { "views": [], "bendAngle", "projectionHeight" },
  "trackerCrossRef": { "trackerItemNo", "revMatch", "changePoint", "applicability" }
}
```

## Current Inventory (10 Smart Drawings)
| Drawing | Type | Part | KC | VP |
|---|---|---|---|---|
| X30E5-J2047-013 | Assembly | IPM COMP (2WD) | 3 | 41 |
| X30E5-J2047-012 | Assembly | PM COMP (AWD) | — | — |
| 030E5-M6737-011 | Component | Lead Frame O TRC | 45 | 5 |
| 030E5-M6738-011 | Component | Lead Frame N TRC | 27 | 2 |
| 030E5-M6739-001 | Component | Lead Frame O GEN | 42 | 5 |
| 030E5-M6740-001 | Component | Lead Frame N GEN | 31 | 2 |
| 030E5-M6741-011 | Component | Lead Frame O VCU | 36 | 5 |
| 030E5-M6742-011 | Component | Lead Frame N VCU | 27 | 2 |
| 030E5-M6743-001 | Component | Lead Frame O RR | 48 | 5 |
| 030E5-M6744-001 | Component | Lead Frame N RR | 31 | 2 |

## Pattern: O vs N Lead Frames
- **O (output)** variants always have higher KC counts than N (neutral) pairs
- **-011 suffix** = MAJ REV (HIRAKAWA approved), **-001 suffix** = M/P DWG (YAMAKAWA approved)
- All share: C1020-1/2H copper, t0.3±0.03, BSY9-5306, Tochigi Site, CATIA V5

## Integration Points
- **fh4s-drawing-check**: Cross-reference smart JSON revisions against DWG plan
- **fh4s-change-point-update**: Link `trackerCrossRef` to Change Point Tracker rows
- **fh4s-honda-response**: Pull specs from smart JSONs into MLC response slides
- **fh4s-sasg-forms**: Auto-populate Form B quality characteristics from KC/VP data
- **fh4s-ingest**: When new PDFs arrive, auto-trigger smart drawing extraction

## Pipeline

```
Receive PDF Drawing (from ingest / manual drop / JP email)
  ↓
Classify Drawing Origin (Keihin KISO / Hitachi / Showa / Nissin Kogyo)
  ↓
Extract Raw Image (pikepdf → PIL Image, CCITT Group 4 decode)
  ↓
Classify Drawing Type (assembly vs component by aspect ratio)
  ↓
Crop Zones (title block, BOM, notes, KC/VP, KISO stamp)
  ↓
Visual Read Crops (vision model reads each zone → populate template fields)
  ↓
Write Smart JSON (<drawing-number>.smart.json alongside PDF)
  ↓
Route
  ├── Smart JSON → inputs/drawings/
  ├── Integration triggers → fh4s-drawing-check, fh4s-change-point-update, fh4s-sasg-forms
  └── Report → chat (extraction summary)
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `extract` / default | "smart drawing", "extract drawing" | Full extraction pipeline → smart JSON |
| `batch` | "extract all drawings", "batch extract" | Extract all PDFs in directory without smart JSONs |
| `validate` | "validate smart JSON", "check extraction" | Verify existing smart JSON against template schema |
| `update` | "update smart drawing" | Re-extract specific zones from updated PDF |
| `redact` | "redact drawing" | Extract then produce redacted version for external sharing |

Default mode extracts a single PDF and writes the smart JSON sidecar.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Smart JSON | `inputs/drawings/<drawing-number>.smart.json` | Co-located with source PDF |
| Lead frame JSONs | `inputs/drawings/lead-frame/<drawing-number>.smart.json` | Sub-directory for components |
| Extraction report | Chat output | Summary of what was extracted |
| Validation report | `ShadowArchive/80-reports/drawing-validate-YYYY-MM-DD.md` | Schema compliance check |

## Fallback Chain

```
1. pikepdf + PIL Image extraction  (primary — handles CCITT Group 4 natively)
   ↓ (pikepdf unavailable)
2. pdfplumber fallback  (limited — cannot render CCITT G4 correctly)
   ↓ (pdfplumber also fails)
3. Vision model reads full PDF page  (no crop zones — less accurate)
   ↓ (PDF unreadable / corrupted)
4. Ask operator  (explicit disclosure: "Cannot extract drawing — please provide data manually")
```

## Error Handling

| Failure | Recovery |
|---------|----------|
| Decompression bomb limit hit | Increase `MAX_IMAGE_PIXELS`; document the size |
| No extractable image in PDF | Check for vector-only PDF (different format); try pdfplumber |
| Zone crop produces blank/empty image | Adjust fractional coordinates; try wider crop |
| Vision model cannot read zone | Try native resolution crop; suggest manual data entry |
| Template schema mismatch | Check template version; update if schema evolved |
| Drawing origin unknown (not KISO) | Flag as unsupported template; suggest manual extraction |
| `.venv` not available | Install pikepdf into system python or create project venv |

## Contract

- **Current scope**: Only KISO/Keihin template is implemented — Hitachi/Showa/Nissin Kogyo are TBD.
- **Do not** modify source PDFs — read-only extraction.
- **Do not** expose KC/VP quality characteristics or dimensional data in customer-facing artifacts.
- **Do not** store drawing content in SHADOW training pairs — classification metadata only.
- **Preserve** Japanese text exactly in notes fields — never translate or transliterate.
- Smart JSONs are **internal engineering data** — do not relay to Dropbox without operator approval.
- When JP DE sends revised drawings, **update** the smart JSON — don’t recreate from scratch.
