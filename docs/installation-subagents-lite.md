# Install pi-subagents-lite for pi

[pi-subagents-lite](https://pi.dev/packages/pi-subagents-lite) brings sub-agents to pi with a schema-first, minimal-token-overhead design — spawn specialized agents in isolated sessions, each with its own tools, extensions, and model, in the foreground or background. Just three tools (`Agent`, `StopAgent`, `AgentStatus`) and no bloated descriptions; steering and continuation, custom agent types, per-model concurrency limits, a live widget with cost tracking, and a watchdog for stuck agents are all managed from `/agents`.

> Requires Node.js >= 18 and pi >= 0.82.0.

## Install

```bash
pi install npm:pi-subagents-lite
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the `Agent` / `StopAgent` / `AgentStatus` tools are session-level capabilities — one install covers every project. Custom agent definitions can still be scoped per-project via `.pi/agents/*.md`, and per-project model/concurrency defaults via `.pi/subagents-lite.json`.

## Verify

In a fresh `pi` session, confirm:

- The three sub-agent tools are available to the model: `Agent` (spawn), `StopAgent` (stop a running or queued agent by ID), `AgentStatus` (list all agents with type, short ID, and status).
- Running `/agents` opens the management menu.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

### Spawning

The LLM calls `Agent` like any other tool:

- `prompt` (required) — the task text.
- `description` — short label for the widget; defaults to the first line of the prompt.
- `agent` — agent type; defaults to `general-purpose`.
- `run_in_background` — return immediately and notify the parent on completion.
- `worktree_path` — any git repository on disk: a worktree of the parent's repo, its main checkout, or a different repo entirely.

`model`, `thinking`, `max_turns`, and `max_tokens` are injected from config and frontmatter — the LLM never passes them. Set them once and forget. Foreground agents don't lock the session and are bound to the parent's interrupt (stopping the parent stops them, partial output preserved); background agents are fully autonomous. Subagents cannot spawn further subagents.

### `/agents` menu

One menu covers everything:

- **Running agents** — view the live conversation, steer mid-task, continue settled agents, stop, clear.
- **Spawn** — manually spawn an agent without an LLM round-trip.
- **Model settings** — global default and per-type model overrides.
- **Concurrency** — per-model slot limits; a per-model limit overrides a per-provider limit, which overrides the `default`. Excess spawns queue until a slot frees.
- **Agent defaults** — thinking, max turns, force-background.
- **System prompt** — prompt mode, `AGENTS.md` inclusion, skills, extensions.
- **Widget** — layout and visibility.
- **Watchdog** — tool and idle timeouts for stuck agents.

### Widget and conversation viewer

Running and recently finished agents show above the editor. `↓`/`↑` highlights an agent, `Enter` opens the live transcript (thinking blocks, tool calls, compaction summaries, results), `Esc` closes. Steer a running agent mid-task with `Enter` in the viewer or `Steer` in the `/agents` menu; settled agents (completed, errored, stopped, turn-limited) can be continued manually from the viewer.

### Custom agent types

Drop a `.md` file into `.pi/agents/` (project), `.agents/agents/` (shared), or `~/.pi/agent/agents/` (global). Frontmatter configures the agent; the body is its system prompt. The name auto-populates the `agent` parameter's enum — nothing to register. On name clash: project > shared > user > built-in (resolved case-insensitively).

```markdown
---
name: security-review
description: Review code for security issues
tools: [read, bash, grep]
extensions: false
skills: false
model: zai/glm-5.2
thinking: high
max_turns: 80
---

You are a security review specialist. Analyze code for vulnerabilities,
focusing on injection flaws, auth bypasses, and insecure defaults.
```

A minimal agent with just `name` and `description` gets everything, same as `general-purpose`. Set restrictions only when you want them. Useful frontmatter keys:

| Key | Purpose |
| --- | --- |
| `tools` / `exclude_tools` | Tool whitelist / blacklist (built-ins like `read`, `bash`; extension tool names; `ext/*` globs). |
| `extensions` / `exclude_extensions` | Which extensions load (hooks and commands). Does not control tool visibility. |
| `skills` / `preload_skills` | Skill whitelist (metadata-only) / dump full SKILL.md content into the system prompt. |
| `model` / `thinking` | `"provider/model-id"` and thinking level; default inherit parent. |
| `max_turns` / `max_tokens` | Soft turn limit (grace turns before hard abort) / max output tokens per response. |
| `hidden` | Hide from the enum; still callable by name. |

Built-in types: `general-purpose` (full session tools) and `Explore` (read-only codebase exploration). They can be overridden by custom agents or disabled from `/agents`.

### Config files

- **Global** — `~/.pi/agent/subagents-lite.json`, managed via `/agents` or edited directly.
- **Project override** — `.pi/subagents-lite.json`: an override layer that may contain only model and concurrency settings (`agent.default`, per-type model overrides, `concurrency`). Effective resolution: session override > project file > global file > built-in default.

### System prompt modes

`systemPromptMode` (default `replace`):

- `replace` — a minimal generic prompt plus the agent's instructions. Lowest cost and most isolated.
- `inherit` — the parent's system prompt plus the agent's instructions.
- `custom` — `~/.pi/agent/subagents-lite-prompt.md` plus the agent's instructions.

When `includeContextFiles` is `true` (default), AGENTS.md files load as shared context before agent instructions, which improves KV cache prefix hits.

### Watchdog and transcripts

The watchdog stops hung agents and notifies the main session on a kill. Two independent checks, both default 45 minutes (`0` disables): `toolTimeoutMinutes` (a single tool call running too long) and `idleTimeoutMinutes` (no tool events or streamed text for this long). Optionally enable output transcripts — streaming to `/tmp/pi-agent-outputs/<agentId>.log` (append-only, `tail -f` friendly) — globally via config or per-agent via the `output_transcript` frontmatter field.

## Uninstall

```bash
pi remove npm:pi-subagents-lite
```

Removes the entry from `~/.pi/agent/settings.json`. Custom agent definitions (`.pi/agents/`, `.agents/agents/`, `~/.pi/agent/agents/`) and config files (`~/.pi/agent/subagents-lite.json`, `.pi/subagents-lite.json`) are left untouched.

## Scope

pi-subagents-lite adds three tools, a live widget, a conversation viewer, and the `/agents` menu — it does not change the main agent's behavior, system prompt, or default tool list. Model, thinking, turn, and token limits are injected from your config, so the schema the LLM sees stays tiny.
