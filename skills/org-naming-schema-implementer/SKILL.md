---
name: Org Naming Schema Implementer
description: Use when analyzing naming patterns across enterprise systems (email, Teams, SharePoint, file servers, drawings, quality documents) and implementing a unified naming schema for better AI agent retrieval and human findability. Trigger on "naming schema", "naming convention", "standardize naming", "org naming", "naming analysis", "SEO for agents", "agentic naming", "file naming system", "cross-system naming".
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: org-naming-schema-implementer
  icon_style: craft-category-glyph-v1
---

# Org Naming Schema Implementer

Analyzes naming patterns across all enterprise surfaces and implements a unified naming schema optimized for both human findability and AI agent retrieval.

## When to Use

- User wants to standardize naming across Outlook, Teams, SharePoint, drawings, quality docs
- User says "naming schema", "naming convention", "SEO for agents", "agentic naming"
- User wants files/emails/documents to be findable by AI agents across systems
- After discovering naming inconsistencies across programs (e.g., "heatsink" vs "heat sink" vs "HEAT SINK IPM")
- When setting up a new program that needs naming standards from day one

## Trigger Phrases

- "naming schema", "naming convention", "standardize naming"
- "org naming", "naming analysis", "cross-system naming"
- "SEO for agents", "agentic naming", "agent-findable"
- "file naming system", "document naming rules"
- "why can't I find..." (indicates naming problem)

## Core Insight

In the AI agent era, naming isn't just for humans anymore. Every filename, email subject, Teams message, and document title is a **search token** that AI agents use for retrieval. A naming schema must optimize for BOTH:

1. **Human readability** — can a person understand what this is from the name?
2. **Agent retrievability** — can an AI find this across email/Teams/SharePoint/file systems using consistent tokens?

The technique: extract real naming data from all surfaces, analyze token frequency and consistency, then design a schema where the SAME term resolves to the SAME concept across every system.

## Workflow

### Phase 1: Surface Scan (Data Collection)

Extract naming data from every enterprise surface:

**Surface 1: Email Subjects (Outlook)**
```javascript
// Extract via browser JavaScript on Outlook Web
(function() {
    var items = document.querySelectorAll('div[role="option"]');
    var results = items.map(i => i.innerText.trim().substring(0, 200));
    return JSON.stringify(results);
})();
```
Tokenize all subjects. Count term frequency. Flag inconsistencies.

**Surface 2: SharePoint File Names**
```python
# From enterprise-knowledge-inventory.json or SharePoint crawl
import json, re
from collections import Counter

filenames = [item['name'] for item in inventory['files']]
tokens = Counter()
for fn in filenames:
    for word in re.findall(r'[A-Za-z0-9]{3,}', fn):
        tokens[word.lower()] += 1
```

**Surface 3: Teams Messages**
Extract via Teams search or meeting-intelligence-db.json. Tokenize message content.

**Surface 4: Drawing Numbers**
From Smart Drawing JSONs — extract drawing numbers, part names, material codes, spec references.

**Surface 5: Kernel Vocabulary**
Read `kernel/vocabulary.md` — this IS the canonical term registry.

**Surface 6: Change Point Tracker**
Column headers + cell values = the actual language people use.

### Phase 2: Token Analysis

For each surface, extract:
1. **Term frequency** — most common terms across all surfaces
2. **Variant map** — same concept, different spellings (heat sink / heatsink / HEAT SINK / HS)
3. **Gap analysis** — terms that exist in one surface but not others
4. **Collision analysis** — same term, different meanings (e.g., "IPM" = Intelligent Power Module vs IPM Case = the plastic housing)

Output a **Token Consistency Matrix**:

```
| Canonical Term | Outlook Variant | SharePoint Variant | Drawing Variant | Kernel Term | Action |
|---|---|---|---|---|---|
| heat-sink | "heatsink" (9x) | "Heat Sink" (5x) | "HEAT SINK IPM" | "Heat sink" | STANDARDIZE → "heat-sink" |
| IPM | "IPM" (12x) | "IPM COMP" | "IPM CASE COMP" | "IPM" | DISAMBIGUATE → "ipm-assembly" vs "ipm-case" |
| FH4S | "FH4S" (8x) | "FH4S" (15x) | "4E0A" | "FH4S" | MAP → FH4S is program, 4E0A is variant prefix |
```

### Phase 3: Schema Design

Design the naming schema with these principles:

**Principle 1: Program-Component-Part Hierarchy**
```
{program}-{component}-{part}-{document-type}-{revision}
```
Example: `FH4S-IPM-heat-sink-feasibility-RevA.md`

**Principle 2: Hyphen-Separated Tokens**
- Hyphens (`-`) not spaces or underscores
- AI tokenizers split on hyphens → each segment is independently searchable
- Humans read hyphens as word boundaries

**Principle 3: Consistent Canonical Terms**
- ONE spelling per concept across ALL surfaces
- Variant map published alongside schema for retroactive cleanup

**Principle 4: Date Format**
- ISO 8601 only: `2026-05-03`
- Never `5/3/26` or `May 3` or `03-May-2026`

**Principle 5: Document Type Suffix**
Every file/email/document gets a type tag:
- `-spec` (specification)
- `-tracker` (spreadsheet/tracker)
- `-report` (report/analysis)
- `-deck` (presentation)
- `-form` (SASG/quality form)
- `-rfq` (RFQ package)
- `-ppap` (PPAP package)
- `-drawing` (engineering drawing)
- `-minutes` (meeting minutes)
- `-memo` (internal memo)

### Phase 4: SEO-for-Agents Optimization

Beyond human naming, optimize for AI retrieval:

1. **Front-load the most searchable term** — AI agents match on first tokens
   - Bad: `Report on FH4S Heat Sink Feasibility 2026-05-03`
   - Good: `FH4S-IPM-heat-sink-feasibility-report-2026-05-03`

2. **Include program code in every filename** — agents filter by program first
   - Never: `feasibility-checklist.xlsx`
   - Always: `FH4S-IPM-heat-sink-feasibility-checklist.xlsx`

3. **Use drawing numbers as file anchors** — agents cross-reference by DWG#
   - `FH4S-IPM-heat-sink-030KC-M3002-feasibility-drawing.html`

4. **Email subject schema**:
   ```
   [{program}] {component}: {action} — {reference}
   ```
   Example: `[FH4S] IPM heat sink: feasibility review — 030KC-M3002`

5. **Teams message schema**:
   ```
   {program}/{component} — {topic}
   ```
   Example: `FH4S/IPM heat sink — packaging spec for ocean freight`

### Phase 5: Implementation

1. **Publish schema** to enterprise kernel (`astemo-ent/data/kernel/global/naming-schema.md`)
2. **Create variant map** for retroactive cleanup (`naming-variant-map.json`)
3. **Add naming check** to kernel-promote verify step
4. **Generate rename proposals** for existing files that violate schema
5. **Train team** — email Kevin/Bryan with schema + examples

### Phase 6: Ongoing Enforcement

- `kernel-promote verify` checks new kernel files against schema
- Agent sessions auto-tag output filenames per schema
- Quarterly audit: scan all surfaces, flag violations, propose corrections

## Key Decisions

- **Hyphens vs underscores**: Hyphens — better for AI tokenization and URL safety
- **Program code format**: Use customer-facing code (FH4S) not internal prefix (4E0A) in filenames; include both in metadata
- **Case**: lowercase for filenames (cross-platform safe); Title Case for email subjects
- **Drawing numbers**: Always include in filename when applicable — agents use these as primary keys

## Gotchas

- **Don't rename files that others reference** — SharePoint links, email attachments, BOM cross-references break
- **Don't change drawing numbers** — these are Honda's system, not ours
- **Don't force retroactive rename of everything** — focus on NEW files going forward
- **Japanese characters in filenames** — preserve them; JP team searches in Japanese
- **SharePoint path length limit** — 400 chars; long names need abbreviation mapping

## Crystallized From

- Session: 2026-05-03
- Original task: Analyzing naming patterns across Outlook emails, SharePoint files, drawing numbers, kernel vocabulary, and Teams messages to design org-wide naming schema for AI agent era
- Pattern extracted: Cross-surface token analysis → variant mapping → unified schema → enforcement tooling
- Key insight: Naming in the AI era must optimize for both human readability AND agent token retrievability — the same term must resolve to the same concept across every system
