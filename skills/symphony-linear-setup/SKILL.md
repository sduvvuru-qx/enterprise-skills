---
name: Symphony Linear Setup
description: Use when setting up OpenAI Symphony to orchestrate Codex agents via Linear issues and GitHub PRs, or when Symphony fails to poll issues, crashes on startup, or WORKFLOW.md config is rejected.
icon: icon.svg
metadata:
  depends_on: [launchd-mise-service]
  category: workflow/process
  family: workflow
  lifecycle: active
  canonical_slug: symphony-linear-setup
  icon_style: craft-category-glyph-v1
---

# Symphony + Linear + GitHub Setup

## Overview

OpenAI Symphony (Elixir/OTP) polls Linear for issues, spawns Codex per issue, and delivers PRs. Correct WORKFLOW.md schema and runtime invocation are the two most common failure points.

## WORKFLOW.md Schema (Critical)

The schema is defined in `lib/symphony_elixir/config/schema.ex`. Common mistakes from the docs vs reality:

```yaml
---
tracker:
  kind: linear
  # api_key: omit — resolved from $LINEAR_API_KEY env var automatically
  project_slug: "61407f8f1167"   # Linear slugId (hex), NOT project name
  active_states:                  # array — NOT ready_state/active_state/review_state
    - "Ready for Agent"
    - "In Progress"
    - "Rework"
  terminal_states:
    - "Done"
    - "Cancelled"
    - "Canceled"
    - "Duplicate"

workspace:
  root: $SHADOW_WORKSPACE_ROOT    # env var reference — resolved at runtime

agent:
  max_concurrent_agents: 5
  max_turns: 30

codex:
  command: $CODEX_BIN app-server  # $VAR expanded by bash -lc, not Elixir
  thread_sandbox: workspace-write

hooks:
  after_create: |
    eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
    git clone git@github.com:<org>/<repo>.git .
    git config user.name "Leo"
    git config user.email "agent@example.com"
---

<Codex prompt body here>
```

### project_slug

Get it from Linear GraphQL API — it's the `slugId` field, not the project name:
```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: $LINEAR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query":"{ teams { nodes { projects { nodes { name slugId } } } } }"}' | python3 -m json.tool
```

### active_states dual role

`active_states` controls BOTH:
1. Which issues Symphony picks up (polling)
2. Whether to continue an in-progress session (checked between turns)

If Codex moves an issue to a state NOT in `active_states`, Symphony stops the session mid-run. Include all states where work should continue: `["Ready for Agent", "In Progress", "Rework"]`.

### env var resolution

| Field | Resolution |
|---|---|
| `tracker.api_key` | Resolved by Elixir via `$VAR` syntax |
| `workspace.root` | Resolved by Elixir via `$VAR` syntax |
| `codex.command` | Passed raw to `bash -lc` — bash expands `$VAR` |

## Build

```bash
git clone https://github.com/openai/symphony ~/.codex/symphony
cd ~/.codex/symphony/elixir
mise trust && mise install
mix local.hex --force && mix local.rebar --force
mix deps.get && mix compile
mix build   # produces bin/symphony escript
```

## Run

Must use `mise exec --` from the elixir directory (so mise finds `.mise.toml`):

```bash
cd ~/.codex/symphony/elixir
mise exec -- ./bin/symphony \
  --i-understand-that-this-will-be-running-without-the-usual-guardrails \
  /path/to/WORKFLOW.md
```

Flag order: `--i-understand...` BEFORE the WORKFLOW.md path. After = ignored by OptionParser.

## launchd (macOS auto-start)

```xml
<key>ProgramArguments</key>
<array>
  <string>/Users/sdluffy/.local/bin/mise</string>
  <string>exec</string>
  <string>--</string>
  <string>/Users/sdluffy/.codex/symphony/elixir/bin/symphony</string>
  <string>--i-understand-that-this-will-be-running-without-the-usual-guardrails</string>
  <string>/Volumes/SHADOW/WORKFLOW.md</string>
</array>
<key>WorkingDirectory</key>
<string>/Users/sdluffy/.codex/symphony/elixir</string>  <!-- required: mise needs .mise.toml -->
```

See `launchd-mise-service` skill for full plist structure.

## Common Mistakes

| Mistake | Fix |
|---|---|
| `ready_state: "Ready for Agent"` | Use `active_states: ["Ready for Agent", ...]` |
| `project_slug: "SHADOW"` (name) | Use `slugId` from API, e.g. `"61407f8f1167"` |
| `--i-understand...` after WORKFLOW.md path | Move flag before path |
| Running `./bin/symphony` directly without mise | Crashes: `erlexec: No such file or directory` |
| `WorkingDirectory` set to wrong dir | mise can't find `.mise.toml`, wrong Erlang version |
| Codex moves issue to "Human Review" (not in active_states) | Session stops — intentional; Human Review is terminal for the agent |

## Codex auth

Symphony inherits the Codex CLI auth from `~/.codex/auth.json`. No separate API key needed if Codex is already authenticated (`auth_mode: chatgpt`).


## Pipeline

```
Intent → Resolve target → Execute → Validate → Report
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Standard output | Normal use |
| `verbose` | Detailed diagnostics | Debugging |

## Artifact Routing

- Reports: `ShadowArchive/80-reports/`

## Contract

- Destructive operations require explicit operator confirmation
- Always dry-run before applying changes

