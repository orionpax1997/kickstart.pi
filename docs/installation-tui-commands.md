# Install pi-tui-commands for pi

[pi-tui-commands](https://pi.dev/packages/pi-tui-commands) is an interactive `/` command registry for pi. Register any TUI tool (lazygit, nvim, htop, btop, k9s, …) as a slash command that **suspends pi while it runs and restores pi when you quit** — no alt-tab, no lost prompt history.

It ships a single `/tuicmd` slash command plus a searchable toggle list that looks and feels like `/scoped-models`. New commands are auto-checked against your `PATH` before they're enabled, so you cannot accidentally register something missing.

## Install

```bash
pi install npm:pi-tui-commands
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the registry and persistence file live under `~/.pi/agent/tui-commands.json` and the slash command is session-level — one install covers every project you open pi in, no per-project setup needed.

Or try it for a single run without installing:

```bash
pi -e npm:pi-tui-commands
```

## Verify

In a fresh `pi` session, run `/tuicmd`. It should open an interactive list — known tools with a ✓/✗ install status and your currently-enabled set, full keyboard-driven picker (no need to type anything).

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Usage

The extension adds one slash command — `/tuicmd` — and an arbitrary number of *generated* slash commands, one per enabled tool.

| Command | What it does |
| --- | --- |
| `/tuicmd` | Open the interactive toggle list (search, toggle ON/OFF, fuzzy-find) |
| `/tuicmd add <name> [cmd]` | Register a custom command (`/tuicmd add lg lazygit` → `/lg` runs `lazygit`) |
| `/tuicmd rm <name>` | Remove a registered command |
| `/<tool>` | The generated command itself — runs the tool (lazygit, nvim, …) |

### In the interactive `/tuicmd` picker

- **↑ / ↓** — move the selection
- **Enter** — toggle the selected tool ON/OFF (binary is checked on toggle-on)
- **/** — fuzzy-search by name
- **Esc** — close the picker

### When you enable a tool

pi-tui-commands runs `which <binary>` first. If the binary is missing you'll see an inline message with a link to report the issue, and the tool stays OFF — the command is never registered.

### When you run `/lazygit` (or whatever you toggled on)

1. The handler calls `ctx.ui.custom()` — pi's TUI exits cleanly (your prompt history and session are preserved in memory).
2. The tool is spawned with `stdio: "inherit"`, so it takes over your terminal exactly as if you'd launched it yourself.
3. When you quit the tool, pi's TUI comes back exactly where it was — same conversation, same scroll position, same prompt.

## Configure

User config and the enabled-set live in **`~/.pi/agent/tui-commands.json`**. The format is plain JSON — feel free to hand-edit, the extension re-reads on every reload:

```json
{
  "enabled": ["lazygit", "htop", "lg"],
  "custom": [
    { "name": "lg", "command": "lazygit" }
  ]
}
```

- `enabled` — the set of binary names that should be auto-registered as `/<binary>` on next startup.
- `custom` — your `/tuicmd add` entries. Anything in `custom` is registered as `/<name>` regardless of whether it appears in the built-in known-tools list.

To reset: `rm ~/.pi/agent/tui-commands.json` and `/reload`.

## Known tools vs custom

Out of the box the picker shows a curated list of common TUI tools (lazygit, nvim, htop, btop, k9s, tig, vim, micro, …) with their binary names and a friendly description. Toggling one ON registers `/<binary>`; toggling OFF unregisters it.

Want something that isn't in the built-in list? Use `/tuicmd add <name> <binary>` (for example `/tuicmd add docker docker`) — it shows up under "custom" in the picker.

## Uninstall

```bash
pi remove npm:pi-tui-commands
```

Removes the entry from `~/.pi/agent/settings.json`. Your enabled set and custom commands in `~/.pi/agent/tui-commands.json` are **left untouched** — if you reinstall later, your registry is picked up again. To clean up completely:

```bash
rm ~/.pi/agent/tui-commands.json
```

## Scope

pi-tui-commands only adds one slash command (`/tuicmd`) plus the per-enabled-tool generated commands. It does not change pi's main agent behavior, system prompt, default tool list, model selection, or session lifecycle — running a registered tool is a UI-level suspension, not a model-level tool call (the agent never sees the bytes the tool produces).
