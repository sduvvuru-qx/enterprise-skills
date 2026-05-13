---
name: OMX Team Headless Bootstrap
description: 'Use when the user needs: Debug and patch headless omx_run_team failures when the MCP team runner diverges from direct omx team behavior, especially around tmux, prompt mode, worktrees, and worker transport.'
icon: icon.svg
metadata:
  category: automation/headless
  family: automation
  lifecycle: active
  canonical_slug: omx-team-headless-bootstrap
  icon_style: craft-category-glyph-v1
---

# OMX Team Headless Bootstrap

Debug and patch headless `omx_run_team_*` failures when the MCP team runner behaves differently from direct `omx team` usage. Use when background swarm launch fails with tmux-leader or dirty-worktree errors even though prompt-mode workers should be supported.

## When to Use
- `omx_run_team_start` or `omx_run_team_wait` fails during startup
- Errors mention `running inside tmux current leader pane`
- Errors mention `leader_workspace_dirty_for_worktrees`
- The operator wants the MCP/background team path fixed, not just a manual tmux workaround

## Workflow
1. Confirm which OMX copy is live.
   - Find `which omx`
   - Resolve the real install path
   - Check running MCP server processes with `ps aux | rg 'team-server|state-server|oh-my-codex'`
2. Trace the actual MCP launch path.
   - Read `dist/mcp/team-server.js`
   - Confirm how it spawns `dist/team/runtime-cli.js`
   - Do not assume the CLI and MCP paths share the same launch assumptions
3. Check launch-mode resolution.
   - Read `dist/team/runtime-cli.js`
   - Verify how `OMX_TEAM_WORKER_LAUNCH_MODE`, inherited `TMUX`, and TTY state interact
   - For MCP/background launches, prefer prompt mode when stdin/stdout are non-TTY
4. Check worktree-mode resolution.
   - Read `dist/team/runtime.js`
   - Verify whether explicit `worktreeMode: { enabled: false }` is actually honored
   - If explicit false is ignored, patch the resolver so single-workspace mode sticks
5. Add focused regressions first.
   - Update `dist/team/__tests__/runtime-cli.test.js` for launch-mode and start-options behavior
   - Update `dist/team/__tests__/runtime.test.js` for runtime-level explicit-disabled worktree behavior
6. Verify in two layers.
   - Run targeted node tests for `runtime-cli.test.js` and affected runtime tests
   - Re-run the actual `omx_run_team_start` MCP path against the same failing scenario
7. If startup advances but workers still fail, isolate worker transport.
   - Check whether failure changed from tmux/worktree guards to `worker_notify_failed`
   - Reproduce the real worker CLI launch outside OMX
   - Test raw non-TTY launch, then PTY-wrapped launch
   - Separate "worker can stay alive" from "runtime can notify worker successfully"

## Key Decisions
- Prompt vs interactive:
  For background MCP runners, use prompt mode when the spawned runtime has non-TTY stdio, even if inherited env still contains `TMUX`.
- Single workspace vs worktrees:
  If the launch is headless/prompt-driven, default to `worktreeMode: { enabled: false }` unless the operator explicitly wants detached worktrees.
- Patch surface:
  Prefer patching the installed `dist/` files actually executed on this host when that is the live runtime, then validate immediately.
- Launch viability vs notify viability:
  A PTY wrapper can prove the worker binary itself is compatible with headless startup, but OMX may still fail later in `notifyWorkerOutcome` or startup-evidence gating. Treat those as separate bugs.

## Gotchas
- `TMUX` can be inherited by long-lived MCP server processes, so raw env checks are not enough.
- Explicit `worktreeMode: { enabled: false }` may be ignored if the runtime only checks `requestedMode?.enabled`.
- MCP servers may be long-lived; code changes in spawned child files can apply immediately, but parent-process logic may still need a restart if it holds the buggy behavior.
- A passing unit test is not enough. Re-run the real MCP team tool path.
- Real Codex differs from fake test binaries.
  Existing prompt-mode tests may pass with stub `codex` executables while the real Codex CLI dies with `stdin is not a terminal`.
- After fixing non-TTY startup, the next failure may become `worker_notify_failed`.
  That usually means launch viability improved and the failure moved into dispatch/notify/state evidence, not that the previous fix was wrong.

## Self-Diagnostic Loop
- Do not stop at the first plausible root cause.
  If a patch plus unit test passes but the live MCP path still fails, assume there is at least one more control point or stale process boundary.
- Distrust inherited environment signals.
  In background systems, `TMUX`, cwd, and other env markers may reflect the parent server, not the effective execution mode of the child runtime.
- Differentiate four layers explicitly:
  1. operator shell
  2. MCP server parent process
  3. spawned runtime-cli child
  4. runtime/team core
  A fix in one layer does not prove the others are aligned.
- Treat "fixed in code" and "fixed in the currently running system" as separate states.
  Always report both.
- When live behavior disagrees with tests, ask:
  - Am I patching the file that actually executes?
  - Is the parent process caching old logic?
  - Did the runtime ignore my explicit option and silently re-enable a default?
  - Did I prove the full path, or only one helper inside it?

## Meta-Heuristics
- Prefer tracing the concrete call chain over searching globally for the error string once the live binary is known.
- Prefer explicit inputs over ambient defaults.
  Headless launchers should pass launch mode and worktree mode deliberately, not rely on auto-detection.
- When a guard fires in production, write the regression as close as possible to that guard and one layer above it.
  This catches both the local bug and the orchestration bug that triggered it.
- If a runtime offers a fallback mode in tests but production never reaches it, the orchestration entrypoint is part of the bug.
- If the failure signature changes after a patch, count that as progress and re-bound the problem before editing again.

## Validation Markers
- Invalidated hypotheses:
  - "tmux leader requirement is the only blocker"
  - "dirty worktree guard is the final blocker"
- Validated findings:
  - background MCP runner now reaches prompt-mode single-workspace startup
  - raw real Codex prompt worker without PTY fails with `stdin is not a terminal`
  - PTY-wrapped real Codex can stay alive with delayed first input
  - latest remaining edge is prompt worker notification / startup evidence, not worker process creation itself

## Output Contract
- Name the exact file paths patched
- State the targeted test command and result
- State whether the real `omx_run_team_*` path was re-tested
- Separate “fixed in code” from “visible in the currently running MCP server”
- If failure moved rather than disappeared, name the new failure explicitly as progress, not regression

## Crystallized From
- Session: 2026-04-17 on JARVIS
- Original task: Update OMX so background swarm activation works from the MCP team runner without requiring an interactive tmux leader or a clean worktree
