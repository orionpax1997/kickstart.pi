# Install pi-mono-context for pi

[pi-mono-context](https://pi.dev/packages/pi-mono-context) adds a Claude Code-style `/context` command to pi — prints the current session's context-window usage inline: a dense colored grid of used vs free context, current model and total token usage, an estimated per-category breakdown, session stats (turns, message count, cache read/write, cost), and estimated extension allocation grouped by source/package.

The report is display-only: the extension's `context` hook filters it out before LLM calls, so it stays visible in your transcript but never consumes future context window.

## Install

```bash
pi install npm:pi-mono-context
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: `/context` is a session-level command — one install covers every project.

## Verify

In a fresh `pi` session:

1. Send a message or two so the session has content.
2. Run `/context` — a colored grid and usage report appears inline in the transcript.
3. Ask the model what the report just said — it can't know; the report never entered its context.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Usage

```text
/context
```

Example output:

```text
Context Usage
     ⛁ ⛁ ⛁ ⛁ ⛁ ⛁ ⛁ ⛁ ⛁ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶   Opus 4.7 (1m context)
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶   30.6k/1m tokens (3%)
                                               ⛁ System prompts: 9k tokens (0.9%)
                                               ⛁ System tools: 13k tokens (1.3%)
                                               ⛁ Messages: 13 tokens (0.0%)
                                               ⛶ Free space: 969.4k tokens (96.9%)
```

When pi exposes source metadata, an `Extension allocation · estimated` section breaks tokens down per extension/package — tools, commands, and custom messages grouped by `sourceInfo`/`customType`:

```text
Extension allocation · estimated
├ figma: 4.8k tokens (0.5%) · tools 4.2k · commands 600 · custom 0
└ linear: 3.1k tokens (0.3%) · tools 2.7k · commands 400 · custom 0
```

### Accuracy notes

- The total comes from pi itself (`ctx.getContextUsage()`) and is exact.
- The per-category breakdown is a best-effort estimate based on session entries, system prompt text, active tool definitions, and simple token heuristics — exact provider tokenization may differ.

## Uninstall

```bash
pi remove npm:pi-mono-context
```

Removes the entry from `~/.pi/agent/settings.json`.

## Scope

pi-mono-context adds one slash command and a display-only report renderer. It adds no LLM tools and does not change the main agent's behavior, system prompt, or default tool list — the report is stripped before every LLM call.
