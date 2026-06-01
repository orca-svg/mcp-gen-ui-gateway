<div align="center">
  <h1>MCP Gen UI Gateway</h1>
  <p><strong>Context-aware public-service GenUI for Claude Desktop — an MCP project in its pretotype phase</strong></p>

  <a href="https://github.com/koi2026/mcp-gen-ui-gateway/actions/workflows/ci.yml"><img src="https://github.com/koi2026/mcp-gen-ui-gateway/actions/workflows/ci.yml/badge.svg" alt="CI"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-Apache--2.0-blue.svg" alt="License"></a>
  <a href="https://pnpm.io"><img src="https://img.shields.io/badge/pnpm-9-orange.svg" alt="pnpm"></a>
  <img src="https://img.shields.io/badge/TypeScript-5-blue?logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/status-pretotype-orange" alt="Status: Pretotype">
  <a href="README.ko.md"><img src="https://img.shields.io/badge/README-한국어-green" alt="한국어"></a>
</div>

---

**MCP Gen UI Gateway** is an open-source [Model Context Protocol (MCP)](https://modelcontextprotocol.io) project building toward context-aware **Generated UI (GenUI)** for Korean public services — rendered as a Claude HTML Artifact, with no extra infrastructure.

It is **early**. What you can run and see today is a **working pretotype** on the [`pretotype/genui-demo`](https://github.com/koi2026/mcp-gen-ui-gateway/tree/pretotype/genui-demo) branch: three fixed persona scenarios that prove the full `Claude → MCP → GenUI Artifact` path end to end. `main` itself holds an early, unverified **G-1 MVP** of the gateway tools. The context-ranking "Matrix" engine, live public-data sources, and federation are the **[roadmap](#roadmap)** — design intent, not shipped capability.

> **Status — Pretotype phase (2026-06).** The runnable demo lives on the **`pretotype/genui-demo`** branch (Stage 0, fixed artifacts). `main` carries the gateway-track skeleton — `schema` · `core` · `mcp-server` (G-1 MVP, pending verification). Everything past Stage 0 — Matrix scoring, live sources, federation — is design intent. Read [What works today](#what-works-today) and the [Roadmap](#roadmap) before assuming a feature exists.

**[한국어 README →](README.ko.md)**

---

## Why MCP Gen UI Gateway?

Korean public services span dozens of portals (Government24, HomeTax, data.go.kr, law.go.kr). A first-time mover — a newlywed, a freelancer, a postdoctoral researcher — faces the same core problem: the same raw information exists, but **what matters and what to do first differs entirely by context**.

The gateway is being designed around three ideas:

| Problem | The intended approach |
|---------|----------------------|
| Too many portals, too much cognitive load | A single MCP tool call returns only the components relevant to the user's situation |
| Rules-based filtering explodes with new personas | A Matrix scoring algorithm $O(i,c) = \sum_f S(i,f) \times W(f,c)$ generalizes across any context *(roadmap: G-2)* |
| GenUI surfaces need trustworthy sourcing | Every rendered block cites the official government API or document it came from |

The pretotype proves the **delivery path and the feel** of that idea with fixed content. The brain that selects content dynamically is deliberately frozen for now — see the [Roadmap](#roadmap).

---

## What works today

An honest inventory — what actually runs versus what is still design intent.

- ✅ **Pretotype — Stage 0** *(branch: `pretotype/genui-demo`)* — exact-tag routing (`[신혼부부]` / `[프리랜서]` / `[박사후연구원]`) returns one of three fixed, self-contained HTML artifacts, opened verbatim as a Claude Artifact. This is the regression-frozen baseline. The same branch also carries **additive experimental slices** (handoff-source validation, rule-based context ranking, a `GenUIResponse` envelope + dynamic renderer) — unit-tested exploration, not a shipped gateway.
- ⚙️ **Gateway G-1 MVP** *(branch: `main`)* — schema-driven MCP tools + Zod contracts + a SQLite store, in `schema` / `core` / `mcp-server`. Implemented but **pending verification and cleanup**.
- 🚧 **Everything else** — the Matrix scoring engine, live public-data sources, federation, and the React consumer renderer — is **roadmap**, not built yet.

Everything below this line describes **where the project is going**, not what already runs.

---

## Vision: how the full gateway will work

> This is the **target architecture**. The Stage 0 pretotype keeps this ranking engine *frozen* and routes by exact tag instead — proving the end-to-end Artifact path before live orchestration exists.

```
User utterance
      │
      ▼
┌──────────────────────┐
│  Context Extractor   │  inferContextVector(utterance)
│  (context-ranking)   │  → ContextVector (7 dimensions):
│                      │    region · lifeEvent · household
└──────────┬───────────┘    workStatus · housing · urgency · risk
           │
           ▼
┌──────────────────────┐
│   Matrix Scorer      │  O(i,c) = Σ S(i,f) × W(f,c)   ← roadmap (G-2)
│   (core package)     │  rankComponentCandidates(vector, pool)
└──────────┬───────────┘
           │  Top-k components selected
           ▼
┌──────────────────────┐
│   GenUI Renderer     │  5-block palette:
│   (demo-ui / MCP)    │  hero · cta · checklist · risk · evidence
└──────────┬───────────┘
           │
           ▼
   Claude HTML Artifact
   (Government24-style UI)
```

### The Matrix algorithm (target design)

$$O(i,c) = \sum_f S(i,f) \times W(f,c)$$

| Symbol | Meaning |
|--------|---------|
| `i` | User intent (the context vector derived from utterance) |
| `c` | UI component candidate (hero, checklist, risk card…) |
| `f` | Feature dimension (urgency, actionability, risk, evidenceConfidence…) |
| `S(i, f)` | How strongly intent `i` activates feature `f` |
| `W(f, c)` | How much feature `f` weights toward component `c` |

**Top-k O(i, c)** would determine which components appear — the rest suppressed. The point: adding a new persona or domain becomes a new **weight vector**, not hundreds of new IF-THEN rules. This is the **G-2** milestone on the [Roadmap](#roadmap); it is not wired into the shipped pretotype.

---

## Demo: three personas, one prompt

> Runs from the **`pretotype/genui-demo`** branch (see [Quick Start](#quick-start--claude-desktop-pretotype)).

The pretotype shows the core concept with **fixed** content: *same situation, different optimal surface*. The differences below are authored, not yet produced by live ranking — that is exactly what the Roadmap turns dynamic.

**Shared prompt:**
> `대전 유성구로 이사 왔어요. 이사 관련 행정·세무·우리 동네 데이터를 한 곳에서 확인하고 싶어요.`
> *(I just moved to Yuseong-gu, Daejeon. I want to see moving-related admin, tax, and local data in one place.)*

| Tag | Persona | Artifact highlights |
|-----|---------|---------------------|
| `[신혼부부]` | Newlywed couple | Jeonse loan status, resident registration, child benefit checklist |
| `[프리랜서]` | Freelancer | Business address update, tax invoice validity, health insurance |
| `[박사후연구원]` | Postdoctoral researcher | Institutional address change, research grant relocation support |

---

## Quick Start — Claude Desktop (Pretotype)

> The runnable pretotype lives on the **`pretotype/genui-demo`** branch, not `main`. The clone command below checks out that branch.

### Prerequisites

- [Claude Desktop](https://claude.ai/download) signed in
- Node.js **22 LTS** or **24 LTS**
- pnpm 9+ (`corepack enable && corepack prepare pnpm@9 --activate`)

> ⚠️ **Avoid Node 26 Current** — `better-sqlite3` requires a prebuilt binary available only for LTS releases.

### 1. Clone and build

```bash
git clone -b pretotype/genui-demo https://github.com/koi2026/mcp-gen-ui-gateway.git
cd mcp-gen-ui-gateway
pnpm install
pnpm --filter pretotype-mcp-gen-ui-gateway build
```

### 2. Register the MCP server in Claude Desktop

Open `~/Library/Application Support/Claude/claude_desktop_config.json` and add:

```json
{
  "mcpServers": {
    "pretotype-mcp-gen-ui-gateway": {
      "command": "node",
      "args": ["<ABSOLUTE_REPO_PATH>/packages/pretotype-server/dist/pretotype-index.js"]
    }
  }
}
```

Replace `<ABSOLUTE_REPO_PATH>` with the output of `pwd` from the repo root, then fully quit and reopen Claude Desktop.

### 3. Add the host instruction

Paste this into the Claude project or conversation:

```
You render a public portal GenUI pretotype.

If the user includes exactly one of [신혼부부], [프리랜서], or [박사후연구원],
call render_pretotype_scenario with { "utterance": "<full user utterance>" }.

Render the returned HTML verbatim as a Claude HTML Artifact. Do not summarize,
rewrite, or redesign it. Official links are handoff URLs only — no login or
submission happens inside the artifact.

If the tag is missing or ambiguous, ask for exactly one of the three tags.
```

### 4. Run a demo prompt

```
[신혼부부] 대전 유성구로 이사 왔어요. 이사 관련 행정·세무·우리 동네 데이터를 한 곳에서 확인하고 싶어요.
```

Claude should open a Government24-style HTML Artifact. The cloned `pretotype/genui-demo` branch ships `docs/claude-desktop-pretotype-connector.md` with the full walkthrough and troubleshooting.

---

## Roadmap

The product grows **one capability per version**. Versions 0.5 and 0.6 share the same GenUI renderer — only *who fetches the data* changes:

```text
0.5   Claude (glue) ─► korean-law-mcp + pretotype-genui ─► GenUI Artifact
0.6   Claude ─► gateway ─► korean-law-mcp ─► GenUI ─► Artifact
```

In **0.5** Claude is the host glue calling two sibling connectors; in **0.6** the gateway orchestrates downstream MCP servers itself, so Claude sees a single connector. (The live status board and domain glossary live on the active working branches, alongside the version-ladder docs.)

| Version | New capability | Orchestration | Connectors | Status |
|---------|----------------|---------------|------------|--------|
| **0** | Fixed public-service artifacts (3 context tags) | — | pretotype | ✅ shipped · frozen |
| **0.5** | korean-law `action_plan` 5-step UX as a GenUI Artifact | Claude as glue (host) | korean-law + pretotype-genui (2) | 🔜 next |
| **0.6** | **Federation** — the gateway becomes an MCP *client* to korean-law-mcp | inside the gateway | gateway (1) | ⬜ planned |
| **G-1–4** | Ranking Pipeline scoring · multi-source · deploy | gateway | 1 | ⬜ planned |

- **"Frozen"** means the Stage 0 artifacts themselves are immutable (regression baseline); the pretotype *family* still grows additively (0.5, 0.6).
- **Federation** is what earns the "Gateway" name: one connector reuses downstream MCP servers (korean-law-mcp first) instead of re-implementing their APIs, overcoming the host limitation that sibling connectors cannot call one another.
- The **Matrix scoring** in the [Vision](#vision-how-the-full-gateway-will-work) is the **G-2** step layered on top of federation — not part of 0.5 or 0.6.

---

## Project Structure

`main` is the gateway track. The runnable pretotype (fixed 3-persona artifacts + the Vite React `demo-ui`) lives on the **`pretotype/genui-demo`** branch, not here.

```
mcp-gen-ui-gateway/  (main branch)
├── packages/
│   ├── schema/         Zod schemas — intent types, component palette, MCP I/O contracts
│   ├── core/           Tool service + early recommender skeleton (G-1 MVP; Matrix planned for G-2)
│   ├── mcp-server/     MCP gateway entrypoint — tools + SQLite store (G-1 MVP)
│   └── browser-assist/ Experimental Playwright boundary, isolated from core
├── docs/
│   ├── git-workflow.md · git-workflow.ko.md   Branch naming, commits, PR rules
│   ├── host-prompts.md                        Host instruction variants
│   └── prd.md                                 Product requirements
├── CONTRIBUTING.md · CONTRIBUTING.ko.md
├── SECURITY.md
└── LICENSE                                    Apache-2.0
```

| Package | Responsibility | Maturity |
|---------|---------------|----------|
| `schema` | Zod type definitions and JSON Schema export | supporting |
| `core` | Tool service + early recommender skeleton, zero external deps; Matrix planned for G-2 | G-1 MVP, unverified |
| `mcp-server` | MCP tool registration, SQLite change log | G-1 MVP, unverified |
| `browser-assist` | Experimental gov24 live-source boundary | experimental, isolated |

---

## Development

```bash
pnpm install        # install all workspace dependencies
pnpm build          # build all packages
pnpm test           # run all tests
pnpm typecheck      # TypeScript type check
pnpm mcp            # run the gateway MCP server (stdio)
pnpm schemas        # export JSON Schemas from Zod definitions
```

> The `dev` (React `demo-ui`) and `pretotype:*` scripts run from the **`pretotype/genui-demo`** branch, where those packages live.

---

## MCP Tools

On `main`, the `mcp-server` registers the gateway G-1 MVP tools. The pretotype-only tools live on the `pretotype/genui-demo` branch.

| Tool | Where | Status |
|------|-------|--------|
| `searchBenefits` | `mcp-server` (main) | ⚙️ G-1 MVP — find benefit candidates from non-identifying profile conditions |
| `getBenefitDetail` | `mcp-server` (main) | ⚙️ G-1 MVP — structured details for a benefit |
| `buildChecklist` | `mcp-server` (main) | ⚙️ G-1 MVP — application preparation items |
| `getApplicationGuide` | `mcp-server` (main) | ⚙️ G-1 MVP — step-by-step application guidance |
| `getChangeLog` | `mcp-server` (main) | ⚙️ G-1 MVP — recorded snapshot and diff events |
| `render_pretotype_scenario` | pretotype branch | ✅ returns a fixed self-contained HTML artifact for one exact tag |
| `compose_dynamic_genui_response` · `render_dynamic_genui_template` | pretotype branch | 🧪 experimental dynamic GenUI slices |

The server does not include an LLM. The MCP host is expected to orchestrate natural language, follow-up questions, and tool calls.

---

## Contributing

Bug reports, feature proposals, architecture RFCs, code, and documentation contributions are welcome.

| Type | How |
|------|-----|
| Bug | [Bug report template](.github/ISSUE_TEMPLATE/bug_report.md) |
| Architecture | [RFC template](.github/ISSUE_TEMPLATE/rfc.md) |
| Code | [CONTRIBUTING.md](CONTRIBUTING.md) · [docs/git-workflow.md](docs/git-workflow.md) |
| 한국어 기여 | [CONTRIBUTING.ko.md](CONTRIBUTING.ko.md) · [docs/git-workflow.ko.md](docs/git-workflow.ko.md) |

See the issue templates under [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE).

---

## Team Roles

| Role | Area | Responsibility |
|------|------|---------------|
| **A** | Source / MCP Adapter | Public data integration (gov24, RSS, SRT), MCP tool schema design |
| **B** | Decision / Matrix / Lens | Intent parsing, Claude API orchestration, weight calibration |
| **C** | Renderer / Demo | GenUI React components (KRDS), demo UI, deployment |

---

## License

[Apache License 2.0](LICENSE) — Copyright 2026 MCP-Gen UI Gateway contributors.
