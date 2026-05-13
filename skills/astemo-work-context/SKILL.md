---
name: Astemo Work Context
description: 'Use when the user needs: Graduated Astemo work context - resolve program, role, org, task, and workflow dimensions before loading program-buried skills (fh4s-*). Reusable across Honda/Ford/BEV programs and roles. Use when work spans programs, when filing paths, org placement, meeting type, or process gate is unclear. Trigger on: astemo context, which program, work context, org chart, role, USGRP1, workflow, task type, not only FH4S, cross-program, who do I report to, where to save file.'
icon: icon.svg
metadata:
  depends_on: [astemo-program-context,astemo-xlsx,fh4s-change-point-update,fh4s-context,fh4s-drawing-query,fh4s-meeting-prep,fh4s-sasg-forms,fh4s-smart-drawing]
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-work-context
  icon_style: craft-category-glyph-v1
---

# astemo-work-context - Graduated context (program · role · org · task · workflow)

**Purpose:** One **cross-cutting** skill so rules are **not buried** inside `fh4s-*` (or other program) skills. Load this **first** when any dimension below is ambiguous; then load **narrow** skills (FH4S tracker, Recorder, Smartsheet, etc.) for facts and procedures.

**Canonical files (repo, stable URLs for other skills to cite):**

| Layer | File |
|-------|------|
| Programs + Dropbox routing | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/PROGRAM-FILE-ROUTING.md` (root symlink `.../PROGRAM-FILE-ROUTING.md`) |
| Machine registry (`dropbox_root`, ids) | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/program-registry.json` (root symlink `.../program-registry.json`) |
| **Cloud registry (live)** | `SHADOW-Programs` list on `USGRPxEV` - see `shadow-lists` skill, use `SL_DASHBOARD` or query `$filter=ProgramID eq 'fh4s'` |
| Human program table, org, vocabulary | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/ASTEMO-CONTEXT.md` (root symlink `.../ASTEMO-CONTEXT.md`) |
| **Bundle index** | `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/ASTEMO-BUNDLE.md` |
| **Legacy path** | `/Volumes/☯Duality/spaces/professional/astemo/` → same folder (**symlink** to `sduvvuru-qx/`). |
| **Mesh parity** | See **`ASTEMO-BUNDLE.md` § Mesh** - `git pull` on each node's Duality checkout; sync `astemo-work-context` / `astemo-program-context` skills; optional `ASTEMO_PROGRAM_REGISTRY` override per host. |

---

## 1) Program (what product / customer / code)

**Resolve first:** explicit user code (`FH4S`, `FH4`, `FH4L`, `XY9H`, `P703`, …) → row in `PROGRAM-FILE-ROUTING.md` + `program-registry.json`.

**Cloud path (preferred when browser is authenticated):** Query `SHADOW-Programs` list on USGRPxEV via `SL_DASHBOARD` or targeted filter:
```
GET /sites/USGRPxEV/_api/web/lists/getbytitle('SHADOW-Programs')/items?$filter=ProgramID eq 'fh4s'&$select=ProgramID,Title,OEM,PStatus,Site,DropboxPath,SmartsheetID,ModelCode
```
Returns live data including any updates since `program-registry.json` was last edited.

**Rules**

- `default_program` in registry is **not** permission to file everything under FH4S.
- **Ford** rows may have `dropbox_root: null` - use declared SharePoint/team storage; do not silently use Honda relay.
- **Gen IV VA** is a roll-up: file under the **contributing** program (FH4S or FH4) unless split explicitly.

**Then** load program-specific skills only when needed (e.g. `fh4s-context` for FH4S part numbers and tracker layout).

---

## 2) Role (whose hat / audience / voice)

**Resolve:** Who the deliverable is **for** (customer Honda vs internal Astemo vs supplier), and **who is speaking** (US DE vs JP vs BUP vs SQ).

**Sabarish role frame**

When the operator is Sabarish and the work is internal Astemo AI/Copilot/RFQ or workflow support, default to this role model:

- Sabarish is translating a concrete engineering need into a controlled workflow path.
- He is not positioned as IT owner, tool reseller, or broad AI platform evaluator.
- He may show tiered options, but should not expose full internal analysis, prompt design, agent scaffolding, or unverified savings assumptions unless explicitly asked.
- Keep DE review and baseline judgment visible as the control gate.
- Show enough work to support alignment, then defer detailed rationale to email, review packet, or measured pilot output.

**Pointers**

- Operator identity and scope: `ASTEMO-CONTEXT.md` §1 **Identity & Role** (adapt names if the session is for another engineer only when user says so).
- Customer-facing tone: `ASTEMO-CONTEXT.md` §14 **Customer Communication Rules**.
- Do **not** conflate "IPM lead" roster lines with unrelated program columns - org tables are **evidence**, not inference (see §5).

---

## 3) Org (site, team label, reporting)

**Resolve:** Site (`USGRP1`, ...), **team label** (e.g. 2nd INV team), manager chain - from **user confirmation**, **Workday**, or **transcript**, not from defaults.

**Pointers**

- Reporting and key people: `ASTEMO-CONTEXT.md` §3-§4.
- **Transcript / Recorder** may update org facts - only promote lines **spoken** or **explicitly confirmed** in chat.

---

## 4) Task (what artifact or operation)

**Map task → skill family** (examples, not exhaustive):

| Task type | Narrow skill / surface (after context) |
|-----------|----------------------------------------|
| Meeting prep / agenda | `fh4s-meeting-prep` (FH4S-shaped) or reuse workflow + correct program paths |
| Transcript / Recorder / Teams | `shadow-recorder`, `astemo-recorder` |
| Change point tracker | `fh4s-change-point-update` (FH4S workbook); other programs: bind tracker path in registry first |
| Drawings / smart JSON | `fh4s-smart-drawing`, `fh4s-drawing-query` |
| SASG forms | `fh4s-sasg-forms` |
| Dropbox / API push | `hl-dropbox` |

If the task does not match FH4S, still use the **same workflow shape** where possible; swap paths via **§1 Program**.

**Recorder identity rule:**
- Program context does **not** change Recorder account routing.
- Google Recorder stays on `sdluffy` / `Default` / `sabarish.duvvuru@gmail.com` unless the operator explicitly says it moved.
- Do not infer `sduvvuru` / Profile 5 for Recorder just because the meeting content is Astemo-related.

---

## 5) Workflow (process gates, order of operations)

**Resolve:** Which quality / program gate applies (MLC, DR/TR, DFMEA, Smartsheet cadence, etc.).

**Pointers**

- Process map: `ASTEMO-CONTEXT.md` §9 **Processes & Workflows**, §10 **SASG**.
- **Do not** invent gate status; tie steps to tracker / transcript / Smartsheet **evidence**.

---

## 6) Skill layering (how to stay DRY)

| Tier | What lives here | Examples |
|------|------------------|----------|
| **A - Cross-cutting (this skill + routing doc + ASTEMO-CONTEXT)** | Program / role / org / task / workflow **resolution** and **rules** | `astemo-work-context`, `PROGRAM-FILE-ROUTING.md` |
| **B - Customer or product package** | Deep **FH4S** (or other) **data**, names, sheet columns, drawing prefixes | `fh4s-context`, `fh4s-meeting-prep`, `fh4s-change-point-update` |
| **C - Tool / channel** | Recorder, Teams, Dropbox API, Excel | `shadow-recorder`, `hl-dropbox`, `astemo-xlsx` |

**Authoring rule:** Tier **B** skills should **link** Tier **A** files instead of duplicating org-wide or multi-program guidance.

---

## 7) Quick registry listing (optional)

```bash
python3 -c "import json; d=json.load(open('/Volumes/☯Duality/spaces/professional/sduvvuru-qx/program-registry.json')); \
  print('default_program:', d.get('default_program')); \
  [print(k, v.get('code'), v.get('dropbox_root'), v.get('status')) for k,v in d['programs'].items()]"
```

---

*Supersedes general "multi-program routing only" guidance formerly scattered; narrow **`astemo-program-context`** remains as a short alias pointing here.*

## Pipeline

```
User Request (ambiguous program / role / path / task)
  ↓
Resolve Program (explicit code → registry lookup → cloud list → ask)
  ↓
Resolve Role (operator identity → audience → voice/tone)
  ↓
Resolve Org (site, team label, reporting chain)
  ↓
Resolve Task (map to skill family: meeting / tracker / drawing / form / deck)
  ↓
Resolve Workflow (quality gates, SASG sequence, DR/TR cadence)
  ↓
Output
  ├── Resolved context (program, role, org, task, workflow) in chat
  ├── Load narrow skill (fh4s-*, astemo-*, hl-*) with correct paths
  └── Route artifact to correct Dropbox/SharePoint path
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `resolve` / default | "astemo context", "which program", "work context" | Full 5-dimension resolution (program, role, org, task, workflow) |
| `program` | "which program", "FH4S or FH4L" | Program resolution only — code, Dropbox root, registry entry |
| `path` | "where to save", "correct folder" | Artifact routing — target Dropbox/SharePoint path |
| `role` | "who am I", "what hat", "audience" | Role and audience resolution |
| `org` | "org chart", "who do I report to" | Org structure and reporting chain |
| `task` | "what skill", "which workflow" | Task → skill family mapping |
| `registry` | "list programs", "program registry" | Quick registry listing with code, status, Dropbox root |

Default output resolves all 5 dimensions when any is ambiguous.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Context resolution | Chat output only | Not persisted — stateless resolution |
| Program registry query | In-memory → chat | Read from `program-registry.json` or cloud list |
| Routing decisions | No durable artifact | Context is transient — load narrow skills for persistence |

This skill produces **routing decisions**, not durable artifacts. Downstream skills handle their own artifact routing.

## Fallback Chain

```
1. Explicit user input  ("FH4S", "for Honda", "internal deck")  — highest confidence
   ↓ (ambiguous or missing)
2. program-registry.json  (local machine registry)
   ↓ (program not in local registry)
3. Cloud list: SHADOW-Programs on USGRPxEV  (live SharePoint list)
   ↓ (cloud list unavailable / VPN down)
4. ASTEMO-CONTEXT.md  (static human-maintained context file)
   ↓ (context file outdated or missing)
5. Ask operator  (explicit disclosure: "Cannot resolve — please specify program/role/task")
```

Always disclose which resolution source was used.

## Prerequisites

- **PROGRAM-FILE-ROUTING.md** accessible — program Dropbox roots and routing rules
- **program-registry.json** accessible — machine-readable program metadata
- **ASTEMO-CONTEXT.md** accessible — human-maintained org, vocabulary, and rules
- **ASTEMO-BUNDLE.md** accessible — bundle index and mesh parity notes
- **SharePoint browser auth** (optional) — for cloud list queries when local registry is stale

## Error Handling

| Failure | Recovery |
|---------|----------|
| Program not in registry | Ask operator to specify; suggest closest match by code |
| Dropbox root is null (Ford) | Route to declared SharePoint/team storage instead |
| Role ambiguous (multiple hats) | Ask operator which hat; default to US DE Lead context |
| Task doesn’t match known skills | List closest skill families; suggest reuse of workflow shape with correct paths |
| Cloud list unavailable | Use local registry; note potential staleness |
| ASTEMO-CONTEXT.md outdated | Use what’s available; flag outdated sections for operator update |

## Contract

- **Do not** use `default_program` as permission to file everything under FH4S.
- **Ford** programs may have `dropbox_root: null` — use declared SharePoint/team storage; never silently route to Honda relay.
- **Gen IV VA** is a roll-up — file under the **contributing** program (FH4S or FH4) unless split explicitly.
- **Do not** invent gate status — tie workflow steps to tracker/transcript/Smartsheet **evidence**.
- **Do not** conflate org table entries with inference — org tables are evidence, not inference.
- **Recorder identity does not change** based on program context — Google Recorder stays on `sdluffy` / `Default` unless operator explicitly says it moved.
- Tier B skills should **link** this skill (Tier A) instead of duplicating multi-program guidance.
