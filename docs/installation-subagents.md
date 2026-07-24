# Install pi-subagents for pi

[@tintinweb/pi-subagents](https://pi.dev/packages/@tintinweb/pi-subagents) brings Claude Code-style autonomous sub-agents to pi — spawn specialized agents in isolated sessions, each with its own tools, system prompt, model, and thinking level. Run them in foreground or background, steer them mid-run, and define your own agent types via `.pi/agents/*.md` (project) or globally.

## Install

```bash
pi install npm:@tintinweb/pi-subagents
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: sub-agent tools (`Agent`, `get_subagent_result`, `steer_subagent`) are session-level capabilities — one install covers every project. Custom agent definitions can still be scoped per-project via `.pi/agents/*.md`.

## Verify

In a fresh `pi` session, confirm:

- The `Agent` tool is available — it shows up in the model's tool list when the main agent decides to delegate work.
- Running `/agents` opens the FleetView — a navigable list of the current main session and any running sub-agents.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

### `/agents` slash command

Open the FleetView to inspect, navigate, and steer running sub-agents:

- `↓` / `←` (at an empty prompt) — jump into the FleetView
- `↑` / `↓` — move the selection between agents
- `Enter` — open the selected agent's live, auto-updating conversation
- `Esc` — return to the main session
- `Enter` on a running agent — open an inline composer to steer it; send with `Enter`, cancel with `Esc` or empty submit
- `x` (then `x` to confirm) — stop a running agent

Finished agents linger briefly in the FleetView before dropping out, and the conversation viewer stays open through completion so you can read the final output.

### Widget visibility

`/agents → Settings → Widget`:

- `all` — show every agent (foreground + background)
- `background` (default) — hide foreground runs; they already render inline as `Agent` tool results
- `off` — disable the persistent widget entirely

### Custom agent types

Define agents in `.pi/agents/*.md` (project) or `~/.pi/agent/agents/*.md` (global), with YAML frontmatter:

```markdown
---
name: reviewer
description: Reviews code for correctness and style
model: sonnet
thinking: medium
---

You are a senior engineer reviewing code for correctness, style, and edge cases.
```

Available frontmatter keys include `name`, `description`, `model` (override the default), `thinking` (thinking level), and `tools` (restrict the agent to a subset of tools). Custom types are auto-discovered and offered to the main agent alongside the built-in types when it spawns sub-agents.

## Uninstall

```bash
pi remove npm:@tintinweb/pi-subagents
```

Removes the entry from `~/.pi/agent/settings.json`. Custom agent definitions in `.pi/agents/` and `~/.pi/agent/agents/` are left untouched.

## Scope

pi-subagents only adds sub-agent tools and the FleetView UI — it does not change the main agent's behavior, system prompt, or default tool list.