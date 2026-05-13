---
name: Astemo Pipeline
description: 'Use when the user needs: Astemo enterprise data pipeline — M365 extraction → kernel enrichment → PE-Framework RAG → shadow-research training pairs. Connects headless skills to the Astemo kernel for metadata enrichment and model training.'
icon: icon.svg
metadata:
  depends_on: [astemo-outlook, astemo-teams, hl-sharepoint]
  filePattern:
  - '**/ASTEMO/**'
  - '**/astemo/**'
  - '**/kernel.json'
  bashPattern:
  - astemo.*pipeline
  - enrich
  - training pairs
  - kernel.*enrich
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-pipeline
  icon_style: craft-category-glyph-v1
---

# astemo-pipeline — Enterprise Data Pipeline

Connects M365 headless extraction → Astemo kernel enrichment → training pair generation.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    DATA SOURCES (M365)                       │
├──────────┬──────────┬──────────┬──────────┬────────────────┤
│ Outlook  │  Teams   │SharePoint│ Recorder │  Smartsheet    │
│(hl-outlook│(hl-teams)│(hl-share │(astemo-  │(astemo-        │
│ astemo-  │ astemo-  │ point    │ recorder)│ smartsheet)    │
│ outlook) │ teams)   │ astemo-  │          │                │
│          │          │ share    │          │                │
│          │          │ point)   │          │                │
└────┬─────┴────┬─────┴────┬─────┴────┬─────┴───────┬────────┘
     │          │          │          │             │
     ▼          ▼          ▼          ▼             ▼
┌─────────────────────────────────────────────────────────────┐
│              LOCAL CACHE (SQLite)                            │
│  emails.db │ messages.db │ files.db │ transcripts.db        │
│  astemo_enterprise.db (unified)                             │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              ASTEMO KERNEL ENRICHMENT                        │
│                                                             │
│  kernel.json ─┐                                             │
│  identity.md  │→ Metadata tags: program, department,        │
│  programs.md  │  part_number, change_point_id, severity,    │
│  standards.md │  sasg_form, drawing_ref, supplier           │
│  processes.md │                                             │
│  vocabulary.md│→ Term normalization: JP↔EN vocabulary,      │
│  tools.md    ─┘  acronym expansion, SASG code lookup        │
│                                                             │
│  Enrichment rules:                                          │
│  1. Tag every record with {program, department, source}     │
│  2. Resolve part numbers → drawing refs                     │
│  3. Map people → roles (from identity.md)                   │
│  4. Classify content: technical/commercial/quality/schedule  │
│  5. Extract action items → astemo-meeting-actions            │
│  6. Flag change points → fh4s-change-point-update           │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              PE-FRAMEWORK RAG LAYER                          │
│  /ASTEMO/tools/pe-framework/                                │
│                                                             │
│  3,731 chunks from 597 files                                │
│  nomic-embed-text + qwen3:8b                                │
│  6 domain modes:                                            │
│    design │ quality │ process │ standard │ drawing │ general │
│                                                             │
│  Enriched records get vectorized and indexed                │
│  Query: "What SASG standard applies to heatsink plating?"   │
│  → Returns relevant chunks + kernel metadata                │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│           SHADOW-RESEARCH TRAINING PAIRS                     │
│                                                             │
│  Format: {question, answer, source, program, domain}         │
│                                                             │
│  Generated from:                                            │
│  - Email Q&A (Bryan asks → Sabarish answers)                │
│  - Meeting transcript decisions                              │
│  - Change point rationale (why this change?)                 │
│  - SASG standard applications                                │
│  - Drawing spec interpretations                              │
│  - Supplier evaluation criteria                              │
│                                                             │
│  Output: /ASTEMO/kernel/training-pairs/                      │
│  Feed to: shadow-research pipeline                           │
│  Target: Astemo SLM fine-tuning                              │
└─────────────────────────────────────────────────────────────┘
```

## Pipeline Operations

### 1. Extract — Pull data from M365

```python
# Pseudo-flow (executed by headless skills)
outlook_emails = astemo_outlook.search("from:voris OR from:butler since:2026-03-20")
teams_messages = astemo_teams.get_channel("INV", limit=50)
sharepoint_files = astemo_sharepoint.list("Hoe-Axle/Engineering/FH4S/")
recorder_transcripts = astemo_recorder.list(date="2026-03-26")
smartsheet_schedule = astemo_smartsheet.get_sheet(2231837406482308)
```

### 2. Enrich — Apply Astemo kernel metadata

For each extracted record, the enrichment layer adds:

| Field | Source | Example |
|-------|--------|---------|
| `program` | programs.md match | `FH4S` |
| `department` | identity.md role map | `DE`, `PMO`, `SQ` |
| `part_numbers` | regex + drawing index | `["4E0A-***-***"]` |
| `change_points` | tracker cross-ref | `["CP-012"]` |
| `sasg_refs` | standards.md lookup | `["X10-02A", "K11-03A"]` |
| `severity` | vocabulary.md | `A` (safety), `B` (legal), `C` (functional) |
| `people` | identity.md | `{"voris": "Sr. Director", "butler": "Program Manager"}` |
| `content_class` | classifier | `technical` / `commercial` / `quality` / `schedule` |
| `language` | detector | `en`, `ja`, `mixed` |
| `source_type` | extractor | `email`, `teams_chat`, `transcript`, `document` |

### 3. Index — Add to PE-Framework vector store

```bash
cd /Volumes/SHADOW/ASTEMO/tools/pe-framework
python ingest.py --source enriched/ --domain auto
```

### 4. Generate Training Pairs

Transform enriched records into Q&A format:

**From emails:**
```json
{
  "question": "What is the path to turn the DE red item to yellow or green on FH4S?",
  "answer": "Split the PMO line item by responsible function. DE scope is limited to BOM structure and change point definition — remaining open points are owned by procurement (supplier quotation) and sourcing (ATECS/Nagase evaluation).",
  "source": "outlook:voris_email_2026-03-19",
  "program": "FH4S",
  "domain": "quality"
}
```

**From meeting transcripts:**
```json
{
  "question": "What coating does GKN recommend for powder metallurgy heatsinks?",
  "answer": "Mid-foss nickel phosphorus — best balance of quality consistency and cost. Low/mid/high foss variants exist; mid-foss is the recommended middle ground for solder attachment applications.",
  "source": "recorder:gkn_metals_2026-03-26",
  "program": "FH4S",
  "domain": "design"
}
```

**From SASG standards:**
```json
{
  "question": "Which SASG form covers DFMEA for IPM localization?",
  "answer": "K11-03A — Design Failure Mode and Effects Analysis. Required for all change points with severity A or B.",
  "source": "standards:K11-03A",
  "program": "FH4S",
  "domain": "quality"
}
```

### 5. Feed shadow-research

```bash
# Copy pairs to research pipeline
cp /Volumes/SHADOW/ASTEMO/kernel/training-pairs/*.jsonl \
   /Volumes/SHADOW/ShadowArchive/10-projects/shadow-research/training-data/astemo/

# Validate
shadow research validate --source astemo
```

## Generalizable Pattern

The pipeline architecture generalizes to ANY enterprise:

| Astemo-Specific | Generalizable Equivalent |
|----------------|--------------------------|
| `astemo-outlook` | `hl-outlook` |
| `astemo-teams` | `hl-teams` |
| `astemo-sharepoint` | `hl-sharepoint` |
| `astemo-recorder` | `hl-recorder` (Google Recorder) |
| Astemo kernel (programs.md, etc.) | Enterprise kernel template |
| PE-Framework (SASG chunks) | Domain-specific RAG |
| Shadow-research pairs | Fine-tuning pipeline |

To adapt for a new enterprise:
1. Create kernel files: identity.md, programs.md, vocabulary.md, standards.md
2. Configure hl-* skills with tenant URLs and Chrome profile
3. Set up PE-Framework with domain documents
4. Run pipeline to generate training pairs

## Shadow-Lab API Integration

All pipeline data is served via:
- `GET /v2/astemo/programs` — program metadata from kernel
- `GET /v2/astemo/actions` — action items from enrichment
- `POST /v2/scatter/scan` — trigger PE-Framework indexing
- `POST /v2/scatter/search` — query enriched content

## Cron Schedule

| Frequency | Operation |
|-----------|-----------|
| Hourly | Extract new emails (flagged/unread) |
| 4x daily | Sync Teams messages from key channels |
| Daily | Index new SharePoint documents |
| After meetings | Extract recorder transcripts |
| Weekly | Generate training pairs, validate, feed pipeline |

## Kernel Update Protocol

When pipeline discovers new information that should update the kernel:
1. New program → update programs.md
2. New person/role → update identity.md
3. New acronym → update vocabulary.md
4. New standard reference → update standards.md
5. Recompute kernel hash: `shadow kernel hash --zone astemo`
