# Installation

Installs kickstart.pi on macOS, Linux, and Windows (via Git Bash / WSL).

## Prerequisites

- [Pi](https://github.com/earendil-works/pi-mono#readme) installed and runnable as `pi`
- Git installed

> Pi requires a bash shell. On Windows, install [Git for Windows](https://git-scm.com/download/win) — pi auto-discovers Git Bash.

If `pi` is not on your PATH yet, install it first:

```bash
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
```

Then `pi --help` to confirm it works.

## Step 1: Backup Existing Config

If you already have a pi config, back it up first.

```bash
mv ~/.pi/agent ~/.pi/agent.bak
```

Skip this step if you have no existing config.

## Step 2: Clone kickstart.pi

```bash
git clone https://github.com/orionpax1997/kickstart.pi ~/.pi/agent
```

## Step 3: Install MCP Servers

kickstart.pi expects three MCP servers in every session: `context7` (library docs), `searchcode` (code search across public repos), and `exa` (web search).

First, install the MCP adapter:

```bash
pi install npm:pi-mcp-adapter
```

Then create `~/.pi/agent/mcp.json`:

```json
{
  "mcpServers": {
    "context7": {
      "url": "https://mcp.context7.com/mcp"
    },
    "searchcode": {
      "url": "https://api.searchcode.com/v1/mcp"
    },
    "exa": {
      "url": "https://mcp.exa.ai/mcp",
      "lifecycle": "eager"
    }
  }
}
```

> **Why `eager` for exa?** Web search is the first thing pi reaches for in fresh sessions — kicking off the connection at startup (rather than waiting for the first tool call) means search results are ready by the time pi actually needs them. The other two are `lazy` (default) since they only fire on explicit lookups.

If you already configured MCPs in Cursor / Claude Code / Codex, prefer `/mcp setup` (in any pi session) to import them rather than hand-writing `mcp.json`.

## Step 4: Start Pi

```bash
pi
```

> kickstart.pi intentionally ships **no `settings.json`**. On first run, pi will guide you through selecting a provider and model. If you already have a backup, you can `cp ~/.pi/agent.bak/settings.json ~/.pi/agent/` to restore it after cloning.

In your first session, run `/mcp` to confirm `context7`, `searchcode`, and `exa` are all listed.

---

## Restore Backup

If something goes wrong and you want to restore your original config:

```bash
rm -rf ~/.pi/agent
mv ~/.pi/agent.bak ~/.pi/agent
```

## Update

To update kickstart.pi to the latest version:

```bash
cd ~/.pi/agent
git pull
```

> ⚠️ If you've made local changes to the tracked files, `git pull` may cause conflicts. Consider forking the repo and maintaining your own version instead.

---

## Optional Extras

kickstart.pi is intentionally bare beyond the install essentials. Two categories:

**Token saving:**

- **[`docs/installation-rtk.md`](installation-rtk.md)** — install [rtk](https://github.com/rtk-ai/rtk) globally to transparently rewrite verbose `bash` commands
- **[`docs/installation-caveman.md`](installation-caveman.md)** — install [caveman](https://github.com/JuliusBrussee/caveman) per project to compress pi's prose output

**Agentic workflow:**

- **[`docs/installation-superpowers.md`](installation-superpowers.md)** — install [superpowers](https://github.com/obra/superpowers) per project for a curated skill set (brainstorming, TDD, planning, code review)
- **[`docs/installation-openspec.md`](installation-openspec.md)** — install [OpenSpec](https://github.com/Fission-AI/OpenSpec) per project for spec-driven development

**Explore:**

- **[`docs/installation-codegraph.md`](installation-codegraph.md)** — install [codegraph](https://github.com/colbymchenry/codegraph) per project to pre-index the codebase as a knowledge graph
- **[`docs/installation-codebase-memory-mcp.md`](installation-codebase-memory-mcp.md)** — install [codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) per project for a SQLite-backed knowledge graph

**Additional features:**

- **[`docs/installation-agent-browser.md`](installation-agent-browser.md)** — install [agent-browser](https://github.com/vercel-labs/agent-browser) per project for browser automation via CDP
- **[`docs/installation-subagents.md`](installation-subagents.md)** — install [@tintinweb/pi-subagents](https://pi.dev/packages/@tintinweb/pi-subagents) globally for Claude Code-style autonomous sub-agents with isolated sessions, mid-run steering, and custom agent types

**Beautification:**

- **[`docs/installation-zentui.md`](installation-zentui.md)** — install [pi-zentui](https://pi.dev/packages/pi-zentui) globally for a Starship-style statusline footer and Opencode-style bordered editor

---

## Next Steps

⭐ If kickstart.pi is useful to you, consider giving it a star:
https://github.com/orionpax1997/kickstart.pi

---

Before you start, take 5 minutes to read through:

- **[`README.md`](../README.md)** — explains the design philosophy and how to customize

kickstart.pi is meant to be understood, not just installed.
