---
name: 'Linear GraphQL API'
description: Use when querying or mutating Linear data via API — creating projects, workflow states, labels, posting comments, or transitioning issue states programmatically without using the Linear UI.
icon: icon.svg
metadata:
  category: dev/code
  family: design
  lifecycle: active
  canonical_slug: linear-graphql-api
  icon_style: craft-category-glyph-v1
---

# Linear GraphQL API

## Overview

Linear exposes a GraphQL API at `https://api.linear.app/graphql`. Authenticate with a personal API key header. All mutations use standard GraphQL mutation syntax.

## Auth

```bash
LINEAR_API_KEY=lin_api_...

curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ viewer { id name } }"}'
```

Personal API keys: Linear Settings → Security & access → Personal API keys.

## Quick Reference

### Get teams + projects + slugId

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query":"{ teams { nodes { id name projects { nodes { id name slugId } } } } }"}'
```

`slugId` is the hex string used in WORKFLOW.md `project_slug` — NOT the project name.

### Create project

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"query\":\"mutation { projectCreate(input: { name: \\\"SHADOW\\\", teamIds: [\\\"$TEAM_ID\\\"] }) { success project { id name slugId } } }\"}"
```

### Get existing workflow states

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"query\":\"{ team(id: \\\"$TEAM_ID\\\") { states { nodes { id name type position } } } }\"}"
```

### Create workflow state

`type` must be a **quoted string**, not an enum literal:

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"query\":\"mutation { workflowStateCreate(input: { name: \\\"Ready for Agent\\\", type: \\\"unstarted\\\", color: \\\"#7C5CBF\\\", teamId: \\\"$TEAM_ID\\\", position: 1.5 }) { success workflowState { id name } } }\"}"
```

State types: `backlog`, `unstarted`, `started`, `completed`, `canceled`

### Create label

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"query\":\"mutation { issueLabelCreate(input: { name: \\\"code\\\", color: \\\"#4CAF50\\\", teamId: \\\"$TEAM_ID\\\" }) { success issueLabel { id name } } }\"}"
```

### Create issue comment

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"query\":\"mutation { commentCreate(input: { issueId: \\\"$ISSUE_ID\\\", body: \\\"PR: https://...\\\" }) { success comment { id } } }\"}"
```

### Transition issue state

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"query\":\"mutation { issueUpdate(id: \\\"$ISSUE_ID\\\", input: { stateId: \\\"$STATE_ID\\\" }) { success issue { id state { name } } } }\"}"
```

## Python helper pattern

```python
import subprocess, json

def linear_query(query, api_key):
    result = subprocess.run(
        ["curl", "-s", "-X", "POST", "https://api.linear.app/graphql",
         "-H", f"Authorization: {api_key}",
         "-H", "Content-Type: application/json",
         "-d", json.dumps({"query": query})],
        capture_output=True, text=True
    )
    return json.loads(result.stdout)
```

## Common Mistakes

| Mistake | Fix |
|---|---|
| `type: unstarted` (unquoted) | `type: "unstarted"` — it's a string input, not enum |
| `project_slug: "SHADOW"` (name) | Use `slugId` from API query |
| `{ team(id: "...") { ... } }` auth fails | Use `{ teams { nodes { ... } } }` — some scoped queries need different access |
| Top-level `{ projects { ... } }` returns 401 | Query via `teams { nodes { projects { ... } } }` instead |


## Pipeline

```
Intent → Parse input → Execute API/tool → Structure output → Return
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Formatted result | Normal use |
| `json` | Raw JSON | Programmatic use |

## Artifact Routing

- Output in chat — no persistent artifacts unless operator requests

## Contract

- Never expose API tokens in output
- Respect rate limits

