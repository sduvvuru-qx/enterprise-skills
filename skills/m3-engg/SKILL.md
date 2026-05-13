---
name: 'M3 Engineering'
description: 'Use when the user needs: M3 Engineering & Technology — design systems research using Tavily. Extract, research, and synthesize web content into structured design specs. Faster, cleaner successor to ishuru/- tavily work.'
icon: icon.svg
metadata:
  install_kind: skill
  version: '2026-04-04'
  owner: theleostark/-
  github_org: theleostark/-
  predecessor: ishuru/- tavily skill
  allowed_tools: Bash(firecrawl:*), Bash(python3:*), Read, Write
  shadow_family: research
  shadow_role: analyzer
  shadow_scope: synthesis
  openclaw_surfaces:
  - codex
  - claude
  category: research/web
  family: design
  lifecycle: active
  canonical_slug: m3-engg
  icon_style: craft-category-glyph-v1
---

# m3-engg

M3 Engineering & Technology — research and design system builder using Tavily.

## What This Does

Extracts web content via **Tavily API** (search, extract, map, crawl), then synthesizes it into structured engineering design systems:
- architecture diagrams
- component specs
- integration patterns
- technical decisions
- trade-offs analysis

## Why This Exists

Replaces the earlier ishuru/- tavily skill with:
- **Tavily-native focus** instead of generic hl-fetch
- **M3-style design output** instead of raw scraped content
- **theleostark/-** GitHub org naming to avoid ishuru/- rework
- **Proper design system artifacts**, not just text dumps

## Quick Start

### 1. Search and Research

```bash
# Simple search query
firecrawl scrape "https://tavily.com/api"

# Multi-query research
python3 -c "
import json
queries = ['Tavily API design patterns', 'AI agent web search benchmarks', 'MCP server architecture']
for q in queries:
    print(f'Query: {q}')
"
```

### 2. Extract and Analyze

```bash
# Extract structured content from a URL
firecrawl scrape "https://www.example.com/technical-docs"

# Research a topic
python3 -c "
import json
research_topic = 'microservices vs monolith design patterns'
print(json.dumps({'topic': research_topic, 'depth': 'comprehensive'}))
"
```

### 3. Synthesize into Design System

```bash
# Generate architecture diagram from research
python3 -c "
import json
design_input = {
    'research_findings': '...',
    'constraints': ['latency < 200ms', 'throughput > 1000 req/s'],
    'quality_attributes': ['reliability', 'maintainability', 'scalability']
}
"
```

## Tavily API Basics

### Available Tools

| Tool | Purpose | Notes |
|-------|---------|--------|
| `/search` | Real-time web search with relevance scoring |
| `/extract` | Content extraction from URLs with structure |
| `/map` | Website mapping and discovery (sitemap-style) |
| `/crawl` | Deep website crawling with follow links |
| `/research` | Multi-step research pipeline with summarization |

### Response Format

All Tavily responses include:

- `answer`: Direct answer or synthesis
- `results`: Search results with relevance scores
- `extractedContent`: Structured data from pages
- `context`: Retrieved context snippets
- `confidence`: Confidence score (0-1)
- `latency`: Request latency in ms

## Design System Output Structure

### Phase 1: Discovery

```json
{
  "phase": "discovery",
  "sources": [
    {
      "url": "...",
      "type": "docs|api|blog|benchmark|case_study",
      "relevance": 0.87
    }
  ],
  "key_findings": ["finding 1", "finding 2", "..."]
}
```

### Phase 2: Analysis

```json
{
  "phase": "analysis",
  "requirements": {
    "functional": ["...", "..."],
    "non_functional": ["...", "..."],
    "constraints": ["...", "..."]
  },
  "alternatives": [
    {
      "name": "Option A",
      "pros": ["...", "..."],
      "cons": ["...", "..."]
    },
    {
      "name": "Option B",
      "pros": ["...", "..."],
      "cons": ["...", "..."]
    }
  ]
}
```

### Phase 3: Synthesis

```json
{
  "phase": "synthesis",
  "recommendation": {
    "approach": "A|B|hybrid",
    "rationale": "...",
    "risks": ["...", "..."],
    "mitigations": ["...", "..."]
  },
  "architecture": {
    "type": "monolith|microservices|event_driven|hybrid",
    "components": ["...", "..."],
    "data_flow": ["...", "..."]
  },
  "artifacts": [
    {
      "type": "diagram|spec|api_contract",
      "location": "./artifacts/...",
      "description": "..."
    }
  ]
}
```

## Integration with SHADOW

Output artifacts map to SHADOW patterns:

- **Design systems** → `ShadowArchive/02-manifests/` as technical manifests
- **Architecture decisions** → `memory/adr/` as ADRs
- **Research findings** → `ShadowArchive/80-reports/` as analysis reports
- **Benchmarks** → `ShadowArchive/10-projects/benchmarks/` for comparative studies

## ACP Usage

When using this skill via ACP/Claude Code, prompts should follow this pattern:

> "Use m3-engg to research and design an X-style architecture for [domain]. Focus on:
>
> 1. What problem are we solving?
> 2. What are the key requirements (functional, non-functional)?
> 3. What are our constraints (tech stack, latency, throughput)?
> 4. What are the alternatives, and what are their trade-offs?
> 5. Synthesize into a clear recommendation with artifacts."

## Local Development

### Project Structure

```
m3-engg/
├── SKILL.md
├── bin/
│   └── m3-research.py      # Tavily research pipeline
├── templates/
│   ├── design-system.json    # Design system artifact template
│   ├── architecture.json    # Architecture diagram template
│   └── adr.md             # Architecture Decision Record template
└── examples/
    └── tavily-usage.md    # Example design systems
```

### Key Scripts

- `bin/m3-research.py` — Main research pipeline
  - Phase 1: Discovery (search/map/crawl)
  - Phase 2: Analysis (requirements, alternatives)
  - Phase 3: Synthesis (recommendation + artifacts)
  - Outputs JSON design system artifacts

## Design System Principles

### 1. Problem-First

Always start with:
- What problem are we solving?
- For whom?
- In what context?

### 2. Evidence-Based

All claims backed by:
- Sources (URLs, benchmarks, case studies)
- Metrics (latency, throughput, cost)
- External validation (industry standards, production use)

### 3. Trade-Offs Explicit

Documented trade-offs:
- Option A vs Option B
- Speed vs accuracy
- Cost vs performance
- Complexity vs maintainability

### 4. Artifact-Driven

Produce concrete artifacts:
- Architecture diagrams
- Component specs
- API contracts
- Integration guides
- Deployment manifests

## Output Locations

### Design Systems

```
/Volumes/SHADOW/ShadowArchive/02-manifests/design-{system}.json
```

### ADRs

```
/Volumes/SHADOW/memory/adr/{nnnn}-decision.md
```

### Research Reports

```
/Volumes/SHADOW/ShadowArchive/80-reports/m3-design-{topic}-{timestamp}.md
```

## Best Practices

### 1. Query Design

- Start broad, then narrow
- Use multiple sources (official docs, benchmarks, case studies)
- Filter by relevance and recency

### 2. Artifact Versioning

- Timestamp all artifacts
- Use semantic versioning when appropriate
- Link artifacts to their source research

### 3. SHADOW Integration

- Write artifacts to canonical SHADOW locations
- Use existing ADR format from `headless-shadow-*` patterns
- Register research findings in `ShadowArchive/80-reports`

### 4. Iteration

- Design systems are living documents
- Update as new constraints emerge
- Archive older versions for historical reference

## Comparison to Predecessor (ishuru/- tavily)

| Aspect | ishuru/- | m3-engg (this) |
|--------|-----------|---------------------|
| GitHub org | ishuru/- | theleostark/- |
| Focus | Generic fetch | M3 design systems |
| Output format | Raw text/markdown | Structured JSON artifacts |
| SHADOW integration | Partial | Full (manifests, ADRs, reports) |
| Tavily integration | Generic hl-fetch | Tavily-native tools |
| Speed | Baseline | Optimized |

## Example Design System Output

```json
{
  "id": "tavily-integration-m3-2024",
  "phase": "synthesis",
  "problem": "How to integrate Tavily web search into an M3 engineering design workflow",
  "recommendation": {
    "approach": "hybrid",
    "rationale": "Use Tavily for discovery/research via m3-engg, but keep hl-fetch as fallback for simple one-off fetches.",
    "risks": ["API rate limits", "service dependency"],
    "mitigations": ["Cache results locally", "Implement fallback to hl-fetch", "Monitor API usage"]
  },
  "architecture": {
    "type": "event_driven",
    "components": [
      "tavily_api_client",
      "m3_research_pipeline",
      "design_system_generator",
      "shadow_artifact_writer"
    ],
    "data_flow": [
      "user_request → m3-engg → tavily/search → design_synthesis → shadow_artifacts"
    ]
  },
  "artifacts": [
    {
      "type": "api_spec",
      "location": "./artifacts/tavily-integration-spec.json",
      "description": "Tavily API integration contract with retry logic"
    },
    {
      "type": "diagram",
      "location": "./artifacts/architecture-plantuml.puml",
      "description": "Event-driven architecture with Tavily integration"
    }
  ]
}
```

## Version History

- `2026-04-04` — Initial release, Tavily-native M3 design systems, theleostark/- org
| `/vfs` | **NEW** — VFS namespace and metadata extraction |
