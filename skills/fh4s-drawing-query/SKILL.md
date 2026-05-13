---
name: FH4S Drawing Query
description: 'Use when querying across Smart Drawing JSON files to answer questions about FH4S IPM drawings — KC/VP counts, BOM lookups, revision status, cross-references, material specs, dimensional comparisons. Use for: "which parts have", "compare drawings", "drawing query", "KC count", "VP items", "BOM lookup", "find drawing", "drawing comparison", "lead frame specs".'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context,fh4s-honda-response,fh4s-smart-drawing]
  priority: 80
  filePattern: '**/*.smart.json'
  bashPattern: smart.json|drawing.query|kc.count|vp.count
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-drawing-query
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# FH4S Smart Drawing Query

## Purpose
Query structured data across all Smart Drawing JSON sidecar files to answer questions about
the FH4S IPM drawing set — without opening PDFs or manually scanning drawings.

## Prerequisites
- Smart Drawing JSONs must exist (created by **fh4s-smart-drawing** skill)
- Load **fh4s-context** for project scope

## Smart Drawing Locations
```
/Volumes/SHADOW/ASTEMO/fh4s/inputs/drawings/
├── X30E5-J2047-013.smart.json        # IPM COMP (2WD assembly)
├── X30E5-J2047-012_PM COMP.smart.json # PM COMP (AWD assembly)
└── lead-frame/
    ├── 030E5-M6737-011_*.smart.json   # O TRC
    ├── 030E5-M6738-011_*.smart.json   # N TRC
    ├── 030E5-M6739-001_*.smart.json   # O GEN
    ├── 030E5-M6740-001_*.smart.json   # N GEN
    ├── 030E5-M6741-011_*.smart.json   # O VCU
    ├── 030E5-M6742-011_*.smart.json   # N VCU
    ├── 030E5-M6743-001_*.smart.json   # O RR
    └── 030E5-M6744-001_*.smart.json   # N RR
```

## Query Patterns

### 1. Load All Smart Drawings
```python
import json
from pathlib import Path

root = Path("/Volumes/SHADOW/ASTEMO/fh4s/inputs/drawings")
drawings = {}
for f in root.rglob("*.smart.json"):
    data = json.loads(f.read_text())
    key = data.get("titleBlock", {}).get("drawingNumber", f.stem)
    drawings[key] = data
```

### 2. Common Queries

**KC/VP summary across all components:**
```python
for key, d in sorted(drawings.items()):
    qc = d.get("qualityCharacteristics", {})
    print(f"{d['titleBlock']['partName']:20s}  KC:{qc.get('kcCount','?'):>3}  VP:{qc.get('vpCount','?'):>3}")
```

**Find parts with KC count above threshold:**
```python
threshold = 40
high_kc = [(k, d) for k, d in drawings.items()
           if d.get("qualityCharacteristics", {}).get("kcCount", 0) > threshold]
```

**BOM lookup — which assembly contains a given part:**
```python
part_no = "6640-0062-2011"
for key, d in drawings.items():
    for item in d.get("bomItems", []):
        if item.get("partNo") == part_no:
            print(f"Found in {key} as item #{item['itemNo']}: {item['name']}")
```

**Revision status check:**
```python
for key, d in drawings.items():
    revs = d.get("revisionHistory", [])
    if revs:
        latest = revs[0]
        print(f"{key}: {latest.get('description', '?')} ({latest.get('ecn', '?')})")
```

**Material comparison:**
```python
for key, d in drawings.items():
    tb = d.get("titleBlock", {})
    mat = tb.get("material")
    if mat:
        print(f"{tb.get('partName', key):20s}  {mat}")
```

**Dimensional comparison (e.g., total span across lead frames):**
```python
for key, d in drawings.items():
    for kc in d.get("qualityCharacteristics", {}).get("kcItems", []):
        if "total span" in kc.get("location", ""):
            print(f"{d['titleBlock']['partName']:20s}  {kc['value']}")
```

### 3. Cross-Reference with Change Point Tracker
The `trackerCrossRef` field in each smart JSON links to the tracker:
```json
"trackerCrossRef": {
    "trackerItemNo": 12,
    "trackerRev": "B",
    "revMatch": "match|mismatch|missing",
    "changePoint": "Yes|No",
    "applicability": "2WD|AWD|Both"
}
```
When these fields are populated, queries can detect:
- Parts where drawing rev differs from tracker rev
- Change-point parts that need extra scrutiny
- Applicability mismatches

### 4. Assembly → Component Traversal
Assembly smart JSONs (013, 012) include `_smartRef` fields pointing to component smart JSONs:
```python
assembly = drawings["X30E5-J2047-013"]
for item in assembly.get("bomItems", []):
    ref = item.get("_smartRef")
    if ref:
        # Load the component smart JSON
        comp = json.loads((root / "lead-frame" / ref).read_text())
        kc = comp["qualityCharacteristics"]["kcCount"]
        print(f"  Item {item['itemNo']}: {item['name']} — {kc} KC items")
```

## Output Formats
- **Table**: For comparisons and summaries (markdown table)
- **Report**: For drawing check results (structured markdown with discrepancies)
- **JSON**: For programmatic consumption (filtered/aggregated smart JSON data)
- **Slide data**: For Honda MLC response prep (extracted specs formatted for PPTX)

## Notes
- All Keihin/KISO drawings are rasterized (no text extraction possible from PDFs)
- Smart JSONs are the single source of truth for structured drawing data
- When JP DE (from shadow-sidecast team-map --program FH4S) sends new revisions, update the smart JSON — don't recreate from scratch
- O (output) lead frames always have higher KC counts than N (neutral) pairs

## Pipeline

```
Load Smart Drawing JSONs (*.smart.json from inputs/drawings/)
  ↓
Accept Query (KC/VP count, BOM lookup, revision, material, dimensions, cross-ref)
  ↓
Execute Query (filter, aggregate, compare across drawings)
  ↓
Format Output
  ├── Table (markdown — comparisons, summaries)
  ├── Report (structured markdown — discrepancies, drawing check results)
  ├── JSON (filtered/aggregated data for programmatic use)
  └── Slide data (extracted specs for Honda MLC response prep)
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `query` / default | "which parts have", "KC count", "drawing query" | Single query result in table or report format |
| `summary` | "drawing summary", "all KC counts" | Summary table across all smart drawings |
| `compare` | "compare drawings", "O vs N" | Side-by-side comparison of two or more drawings |
| `bom` | "BOM lookup", "which assembly contains" | BOM traversal — assembly → component or component → assembly |
| `rev-check` | "revision status", "rev check" | Revision cross-reference against tracker/DWG plan |
| `xref` | "cross-reference", "tracker match" | trackerCrossRef field queries — rev mismatches, change-point flags |
| `export` | "export for slides", "MLC data" | Formatted data for Honda MLC/PPTX consumption |

Default output is a single query result in the most readable format.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Query results | Chat output | Transient — not persisted |
| Drawing check report | `ShadowArchive/80-reports/drawing-check-YYYY-MM-DD.md` | Durable cross-reference report |
| Export data (for slides) | → `fh4s-honda-response` | Formatted specs for PPTX |
| Aggregated JSON | `outputs/extracted_text/drawing-query-YYYY-MM-DD.json` | If operator requests persistence |

## Fallback Chain

```
1. Smart Drawing JSONs (*.smart.json)  (primary — structured, queryable)
   ↓ (no smart JSON exists for a drawing)
2. fh4s-smart-drawing extraction  (generate smart JSON from PDF first)
   ↓ (PDF unavailable or unreadable)
3. Change Point Tracker drawing columns  (partial data — rev, part number, status)
   ↓ (tracker data also missing)
4. Ask operator  (explicit disclosure: "No drawing data available for <part>")
```

Always disclose whether data came from smart JSON (high confidence) or fallback source.

## Error Handling

| Failure | Recovery |
|---------|----------|
| No smart JSONs found | Suggest running `fh4s-smart-drawing` extraction first |
| JSON schema mismatch | Check template version; re-extract if template updated |
| Part number not found | List available drawings; suggest closest match |
| trackerCrossRef fields empty | Note data gap; suggest running cross-reference update |
| Massive image decompression | Already handled by `MAX_IMAGE_PIXELS` in extraction; query-only is safe |

## Contract

- **Read-only** — never modify smart JSON files; re-run `fh4s-smart-drawing` for updates.
- **Do not** expose KC/VP counts or quality characteristic details in customer-facing artifacts.
- **Do not** store drawing specs in SHADOW training pairs — classification metadata only.
- Smart JSONs are the **single source of truth** for structured drawing data.
- When JP DE sends new drawing revisions, update the smart JSON via `fh4s-smart-drawing` — don’t recreate from scratch.
- Query results are **internal engineering data** — do not relay to Dropbox without operator approval.
