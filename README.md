# kickstart.pi

A well-commented, ready-to-use starting point for [Pi](https://github.com/earendil-works/pi-mono) that helps you actually understand what's happening.

> Inspired by [kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim) and the opencode version ([kickstart.opencode](https://github.com/orionpax1997/kickstart.opencode)).

[English](README.md) | [简体中文](README.zh-cn.md)

---

## Table of contents

- [Philosophy](#philosophy)
- [Quick start](#quick-start)
- [Project structure](#project-structure)
- [How to make it yours](#how-to-make-it-yours)
- [What this is NOT](#what-this-is-not)
- [Built-in](#built-in)
- [Beautification](#beautification)
- [Saving tokens](#saving-tokens)
- [Agentic Workflow](#agentic-workflow)
- [Explore](#explore)
- [Remote control](#remote-control)
- [Additional features](#additional-features)
- [AGENTS.md](#agentsmd)

---

## Philosophy

Most "AI config starter" repos give you a finished product. You get power, but you don't understand what's happening or why.

**kickstart.pi does the opposite:**

- Every file is short and heavily commented
- Every decision is explained, not just shown
- It's a starting point, not a framework
- You're expected to add your own settings, delete what's unused
- No bundled skills, no bundled agents — you grow into them

---

## Quick start

**Let pi install it for you** (recommended)

Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation.md
```

**Manual installation**

> ⚠️ If `~/.pi/agent/` already has config, back it up first.

```bash
git clone https://github.com/orionpax1997/kickstart.pi ~/.pi/agent
pi
```

Then start pi in any project:

```bash
cd /path/to/project
pi
```

---

## Project structure

```
kickstart.pi/
│
├── LICENSE
├── README.md            ← you are here
├── README.zh-cn.md      ← Chinese version
└── docs/
    ├── installation.md        ← install / backup / update
    ├── installation-rtk.md    ← optional: token-saving bash rewriter (global)
    ├── installation-caveman.md   ← optional: token-saving prose rewriter (project)
    ├── installation-matt-pocock-skills.md   ← optional: mattpocock/skills for real engineers (project)
    ├── installation-superpowers.md  ← optional: agentic workflow skills (project)
    ├── installation-openspec.md   ← optional: spec-driven dev workflow (project)
    ├── installation-codegraph.md   ← optional: pre-indexed code knowledge graph (project)
    ├── installation-codebase-memory-mcp.md   ← optional: SQLite knowledge graph (project)
    ├── installation-agent-browser.md   ← optional: browser automation via CDP (project)
    ├── installation-subagents.md   ← optional: Claude Code-style sub-agents (global)
    ├── installation-permission-system.md   ← optional: deterministic allow / ask / deny gates for tools, bash, MCP, skills (global)
    ├── installation-remote-pi.md   ← optional: local agent mesh + mobile app (global)
    ├── installation-pi-web.md   ← optional: local browser UI over pi sessions (global)
    ├── installation-open-tui.md   ← optional: animated header + Starship footer + rounded editor (global)
    ├── installation-themes-bundle.md   ← optional: sixteen terminal palettes (global)
    ├── installation-rounded-tools.md   ← optional: rounded corners on built-in tools (global)
    └── installation-tui-commands.md   ← optional: turn TUI tools (lazygit, nvim, htop, …) into slash commands (global)
```

That's it. No `settings.json`, no skills, no agents, no extensions. **kickstart.pi is intentionally bare** — your model, theme, and other tooling are configured by you in your own `~/.pi/agent/`. The only required add-on is the three MCP servers installed via Step 3 of [`docs/installation.md`](docs/installation.md) (`context7`, `searchcode`, `exa`).

---

## How to make it yours

1. **Read `README.md` from top to bottom** — understand the philosophy and what's available.
2. **Decide what goes in your `~/.pi/agent/settings.json`** — kickstart.pi ships no config by design. Set your preferred provider, model, and theme. pi walks you through this on first launch.
3. **Install token-savers globally** — [rtk](docs/installation-rtk.md) is the only one recommended at the global level. Compresses verbose bash output across every project.
4. **Install project-level tools as you need them** — [codegraph](docs/installation-codegraph.md) / [codebase-memory-mcp](docs/installation-codebase-memory-mcp.md) for code context, [mattpocock/skills](docs/installation-matt-pocock-skills.md) or [superpowers](docs/installation-superpowers.md) for skills, [OpenSpec](docs/installation-openspec.md) for spec-driven dev, [caveman](docs/installation-caveman.md) for prose compression, [agent-browser](docs/installation-agent-browser.md) for browser automation. Each one scopes itself to the project you `cd` into.
5. **Or install global session-level tools** — [pi-subagents](docs/installation-subagents.md) spawns Claude Code-style sub-agents; [@gotgenes/pi-permission-system](docs/installation-permission-system.md) gates every tool, bash, MCP, and skill call against a single policy file; [pi-tui-commands](docs/installation-tui-commands.md) turns your favourite TUI tools into slash commands that suspend and restore pi. For front-ends on top of pi itself — a browser tab ([pi-web](docs/installation-pi-web.md)) or a phone app ([remote-pi](docs/installation-remote-pi.md)) — see [Remote control](#remote-control). One install covers every project.
6. **Customize the global `AGENTS.md`** — add your language preference, working style, and MCP usage hints that apply everywhere.
7. **Add project-level `AGENTS.md`** in repos that need it — project structure, tech stack, coding conventions.
8. **Create your own prompt templates** in `.pi/prompts/` for repetitive workflows (`/your-command`).

Delete anything you don't use. It's a starting point, not a framework.

---

## What this is NOT

- Not a multi-agent orchestration system
- Not a production-ready AI pipeline
- Not something you use without reading

---

## Built-in

The install flow in [`docs/installation.md`](docs/installation.md) sets up everything kickstart.pi needs to be useful out of the box:

> **Pi's native model is "no MCP".** Pi is built around extensions and skills loaded directly into its own process — MCP isn't part of the core architecture.
>
> **Why an MCP bridge ships anyway:** the broader agent ecosystem (Context7, SearchCode, Exa, and most third-party agent tools) speaks MCP. `pi-mcp-adapter` plus a handful of MCP servers is how kickstart.pi stays compatible with that ecosystem, without taking a stance.

**MCP bridge** (Step 3 prerequisite):

- **pi-mcp-adapter** — adapter package that lets pi talk to MCP servers

**Three required MCP servers** (Step 3):

- **context7** — library and framework documentation
- **searchcode** — public repository code search
- **exa** — web search (loaded eagerly at session start)

**No `settings.json` shipped** — pi walks you through provider / model / theme selection on first launch. If you already have a backup, restore it with `cp ~/.pi/agent.bak/settings.json ~/.pi/agent/`.

These MCP servers are what the global `AGENTS.md` points at. Without them, those instructions have nothing to query.

---

## Beautification

### pi-open-tui

[pi-open-tui](https://pi.dev/packages/pi-open-tui) is a polished TUI styling extension for pi — bundles the best of `pi-haiku`, `pi-claude-code-tui`, and `pi-zentui` into one cohesive package: an animated 16-frame Pi logo header, a two-line [Starship](https://starship.rs/)-inspired footer (cwd, git branch & status, runtime version, context bar, model, tokens, cost), a rounded editor with accent rail, a working timer, and turn telemetry (TPS / TTFT / stalls / cost) shown as a transient notification after each run. Configure header, footer segments, icon mode, and telemetry from inside pi with `/open-tui`.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-open-tui.md
```

Then `/open-tui` to toggle the header / footer / rounded editor, switch between Nerd Font and ASCII icons, and configure telemetry segments.

### pi-themes-bundle

[@firstpick/pi-themes-bundle](https://pi.dev/packages/@firstpick/pi-themes-bundle) adds sixteen custom terminal palettes to pi's theme picker — dark and light variants of Catppuccin, Dracula, Tokyo Night, Gruvbox, Nord, Rosé Pine, One Dark, Solarized, and Everforest, plus `matrix` and `crimson-noir`. Pick one from `/settings` or set `theme` in `~/.pi/agent/settings.json`.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-themes-bundle.md
```

Then `/settings` to pick a palette, or set `theme` in `~/.pi/agent/settings.json` (e.g. `"theme": "tokyo-night"`).

### pi-rounded-tools

[pi-rounded-tools](https://github.com/orionpax1997/pi-rounded-tools) is a minimal cosmetic tweak — it swaps the square corners `┌┐└┘` on pi's built-in tools (`read`, `write`, `edit`, `bash`, `grep`, `find`, `ls`) for rounded ones `╭╮╰╯`. No shell, no extra theme logic; border color just follows your theme's `border` token (yellow while running, red on failure, theme-default on success).

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-rounded-tools.md
```

Then restart pi (or `/reload`) and every built-in tool frame is rounded.

---

## Saving tokens

### rtk

[rtk](https://github.com/rtk-ai/rtk) transparently rewrites verbose shell commands (`git status`, `pnpm list`, `vitest`, `cargo test`, …) into compact token-saving form. Each pi session is intercepted and rewritten automatically — same workflow, fewer tokens.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-rtk.md
```

### caveman

[caveman](https://github.com/juliusbrussee/caveman) compresses pi's prose output ~65–75% while preserving technical accuracy. Six intensity levels, triggered by `/caveman` or keywords like "caveman mode" / "less tokens".

Install at the **project level**. `cd` into the project directory first, then paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-caveman.md
```

---

## Agentic Workflow

### mattpocock/skills

[mattpocock/skills](https://github.com/mattpocock/skills) is Matt Pocock's curated set of engineering skills — grilling interviews, TDD, diagnosing bugs, code review, architecture surveys, triage, and more. Skills auto-load per task; some also register as `/skill:<name>` slash commands. Designed to be small, easy to adapt, and composable.

Install at the **project level**. `cd` into the project directory first, then paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-matt-pocock-skills.md
```

### superpowers

[superpowers](https://github.com/obra/superpowers) provides a curated skill set for brainstorming, debugging, TDD, planning, code review, and more. Skills auto-load per task — no manual invocation once installed.

Install at the **project level**. `cd` into the project directory first, then paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-superpowers.md
```

### OpenSpec

[OpenSpec](https://github.com/Fission-AI/OpenSpec) is a spec-driven development tool for generating and managing project specifications.

Install at the **project level**. `cd` into the project directory first, then paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-openspec.md
```

---

## Explore

### codegraph

[codegraph](https://github.com/colbymchenry/codegraph) pre-indexes the codebase into a knowledge graph. The agent gets precise context — call chains, blast radius, related symbols — in one query.

Install at the **project level**. `cd` into the project directory first, then paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-codegraph.md
```

### codebase-memory-mcp

[codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) is a single static binary that indexes the codebase into a persistent SQLite knowledge graph (158 languages, 14 MCP tools, millisecond queries). Like codegraph, it gives the agent one call for structured context.

Install at the **project level**. `cd` into the project directory first, then paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-codebase-memory-mcp.md
```

---

## Remote control

Drive pi from a different surface — a browser tab or a phone. Both tools are independent processes that read / write the same on-disk pi state (`~/.pi/agent/sessions`, settings, peers); pi itself never knows they're there, and turning them off leaves pi exactly as it was.

### pi-web

[pi-web](https://github.com/agegr/pi-web) is a local web UI for pi. Run `pi-web` and a browser workspace opens at <http://127.0.0.1:30141> — session browser on the left, file tree and source / image / PDF preview on the right, live conversation in the centre, model configuration and skill toggles in the top bar. It reads the same `~/.pi/agent/sessions/*.jsonl` files pi writes, so you can resume any past conversation or fork a new branch from an earlier turn. No extension to install; pi-web is a standalone Node CLI that binds `127.0.0.1` by default and accepts optional Basic Auth via `PI_WEB_PASSWORD`.

Install at the **global** level (it's a CLI that reads your machine-wide pi agent dir). Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-pi-web.md
```

Then run `pi-web` and open <http://127.0.0.1:30141> in your browser. Stop the process and pi keeps running unaffected — pi-web holds no state of its own.

### remote-pi

[remote-pi](https://pi.dev/packages/remote-pi) adds two superpowers on top of pi, wired up by a single `/remote-pi` slash command: a **local agent mesh** (open several pi terminals in the same directory and they discover each other over a Unix-domain-socket broker, with two new LLM tools — `agent_send` and `agent_request`), and a **mobile app** that drives pi from your phone via a QR-paired WebSocket relay (send prompts / voice / images, switch model and thinking level). The agent network is purely local; only the mobile relay touches the network, and only through end-to-end-encrypted payloads.

> remote-pi is unrelated to [pi-subagents](#subagents): sub-agents are spawned **inside one process** by the main agent; remote-pi peers are **separate pi processes** that opt in to a shared mesh and talk to each other directly.
>
> remote-pi is also independent from [pi-web](#pi-web): pi-web reads the **already-written** pi sessions and renders them in a browser tab; remote-pi's mobile app pushes **live** prompts into a running pi session. They're complementary, not alternatives.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-remote-pi.md
```

Then `/remote-pi` to run the one-time setup wizard, `/remote-pi pair` to scan a QR with the [Remote Pi app](https://remote-pi.jacobmoura.work/), and `/remote-pi status` to see who's connected.

---

## Additional features

### subagents

[@tintinweb/pi-subagents](https://pi.dev/packages/@tintinweb/pi-subagents) brings Claude Code-style autonomous sub-agents to pi — spawn specialized agents in isolated sessions, each with its own tools, system prompt, model, and thinking level. Run them in foreground or background, steer them mid-run, and define your own agent types via `.pi/agents/*.md` (project) or globally.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-subagents.md
```

Then `/agents` to manage and inspect running sub-agents from inside pi.

### pi-permission-system

[@gotgenes/pi-permission-system](https://pi.dev/packages/@gotgenes/pi-permission-system) is a deterministic permission gate that sits between the agent and every tool, bash, MCP, and skill call. Three states (`allow` / `deny` / `ask`) and four layered surfaces (`path` → `external_directory` → per-tool patterns → `bash` patterns) cover the vast majority of what a coding agent can do — with UI confirmation dialogs for anything you didn't pre-approve. Sub-agent `ask` prompts are forwarded back to the parent's UI, so sub-agent operations are gated by the same rules.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-permission-system.md
```

Then edit `~/.pi/agent/extensions/pi-permission-system/config.json` to define your policy — start with the safe defaults shipped in the install guide (deny `.env`, `ask` bash, `ask` outside-cwd) and tighten from there.

### agent-browser

[agent-browser](https://github.com/vercel-labs/agent-browser) lets the pi agent control a browser via CDP (Chrome DevTools Protocol) — more token-efficient than traditional headless browser approaches.

Install at the **project level**. `cd` into the project directory first, then paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-agent-browser.md
```

### pi-tui-commands

[pi-tui-commands](https://pi.dev/packages/pi-tui-commands) turns any TUI tool on your `PATH` into a slash command: `/lazygit`, `/nvim`, `/htop`, `/k9s`, … `/tuicmd` opens a searchable toggle list (binary-availability is checked on toggle-on, so you never register something missing); `/tuicmd add lg lazygit` registers a custom alias; the generated command calls `ctx.ui.custom()` to **suspend pi cleanly while the tool runs and restores the TUI on exit** — same conversation, same prompt history, no alt-tab.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-tui-commands.md
```

Then `/tuicmd` to open the picker, `Enter` to flip tools on / off, `/` to fuzzy-search. Your enabled set and custom commands survive restarts in `~/.pi/agent/tui-commands.json`.

---

## AGENTS.md

AGENTS.md is the global instruction file pi loads into every session. Keep it short — only include what truly applies everywhere.

pi also auto-discovers `AGENTS.md` / `CLAUDE.md` walking up from the cwd, so a project-level copy in the project root overrides the global one for that project.

**Suggested content for the global file:**

- **Language preference** — e.g. `Reply in Chinese.`
- **MCP usage hints** — e.g. `Use context7 to look up library and framework documentation.`
- **Personal coding preferences** — e.g. `Never use any type. Prefer explicit types.`
- **Working style** — e.g. `Keep responses concise. No need to explain obvious steps.`

**Project-level `AGENTS.md`** goes in the project root, describing project structure, tech stack, development standards, etc. It overrides the global for that project.
