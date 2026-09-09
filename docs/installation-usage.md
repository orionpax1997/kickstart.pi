# Install pi-mono-usage for pi

[pi-mono-usage](https://pi.dev/packages/pi-mono-usage) adds a `/usage` command that parses your local pi session files and renders an inline dashboard of what your agent usage actually looks like: token spend and cost by provider/model, cost-driver patterns, per-tool stats, a GitHub-style activity heatmap with streaks, and an environmental footprint estimate. Everything is computed locally from `~/.pi/agent/sessions/` — nothing leaves your machine.

## Install

```bash
pi install npm:pi-mono-usage
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: `/usage` aggregates your machine-wide session dir (`~/.pi/agent/sessions/`), so one install covers every project.

## Verify

In a fresh `pi` session, run `/usage` — the dashboard opens inline with a Summary view of your usage so far.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Usage

```text
/usage
```

Five views, switch with `v` or `1`–`5`:

| View | What it shows |
| --- | --- |
| **Summary** | Totals, top providers, and an environmental footprint estimate (kWh, kg CO₂e, real-world equivalences). |
| **Providers** | Per-provider table expanding into per-model rows — session/call counts, cost, and token breakdown (input, output, cache). |
| **Patterns** | Cost drivers for the period: parallel sessions, oversized contexts, large uncached prompts, marathon sessions, top-session concentration. |
| **Tools** | Per-extension table expanding into per-tool rows — call counts, estimated result tokens, session reach. |
| **Activity** | GitHub-style contribution heatmap plus lifetime total, peak day, current streak, and longest streak. |

### Keybindings

| Key | Action |
| --- | --- |
| `Tab` / `←` / `→` | Cycle period: Today, This Week, This Month, All Time |
| `v` / `1`–`5` | Cycle / jump directly to a view |
| `m` | Toggle tokens / cost (Activity view) |
| `↑` / `↓` | Move cursor (Providers, Tools views) |
| `Enter` / `Space` | Expand / collapse a row |
| `q` / `Esc` | Close the panel |

Each period is computed once on open from the same parsed dataset, so cycling is instant. The Activity view ignores the period selector and always spans your full history.

### Data source

Reads `~/.pi/agent/sessions/**/*.jsonl` (or `$PI_CODING_AGENT_DIR/sessions`) plus team-mode worker transcripts. Costs and token counts come from `usage` blocks on `assistant` messages; duplicate turns from branched session files are deduplicated by fingerprint. The footprint estimate feeds the period's charged tokens into [impact-equivalences](https://www.npmjs.com/package/impact-equivalences) — illustrative, not audited accounting.

## Uninstall

```bash
pi remove npm:pi-mono-usage
```

Removes the entry from `~/.pi/agent/settings.json`. Your session files are left untouched.

## Scope

pi-mono-usage adds one slash command and an inline dashboard. It adds no LLM tools and does not change the main agent's behavior, system prompt, or context — it reads session files from disk when you open the dashboard.
