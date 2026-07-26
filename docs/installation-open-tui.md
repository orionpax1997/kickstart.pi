# Install pi-open-tui for pi

[pi-open-tui](https://pi.dev/packages/pi-open-tui) is a polished TUI styling extension for pi that bundles the best of `pi-haiku`, `pi-claude-code-tui`, and `pi-zentui` into one package: an animated 16-frame Pi logo header, a two-line [Starship](https://starship.rs/)-inspired footer (cwd, git branch & status, runtime version, context bar, model, tokens, cost), a rounded editor with accent rail, working-timer + turn telemetry (TPS / TTFT / stalls), and an interactive `/open-tui` settings UI.

## Install

```bash
pi install npm:pi-open-tui
```

This adds the package to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the header, footer, and editor chrome are session-level UI — one install covers every project you open pi in, no per-project setup needed.

Or try it for a single run without installing:

```bash
pi -e npm:pi-open-tui
```

## Verify

In a fresh `pi` session, confirm all three are visible:

- An **animated header** at the top of the TUI cycling through 16 color frames of the Pi logo, plus a "Let's build something great" tagline.
- A **two-line footer** showing your cwd, git branch & status, runtime version, context bar, model, token counts, and cost.
- A **rounded editor** with an accent rail and rounded corners framing the input.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

In a pi session, run `/open-tui` to open the tabbed settings dialog. It has four sections you can cycle with `Tab` / `Shift+Tab`:

- **Features** — toggle the header, footer, rounded editor, telemetry, etc.
- **Icons** — `auto` (detect Nerd Font), `nerd` (force Nerd Font glyphs), or `ascii` (plain fallbacks)
- **Segments** — show / hide individual footer segments (cwd, git branch, git status, git commit hash, runtime, context, tokens, cost)
- **Telemetry** — toggle the post-turn notification and its segments (TPS, TTFT, duration, tokens, stalls, cost)

User config is persisted at `~/.pi/agent/open-tui.json`. Missing or invalid values fall back to open-tui defaults.

## Uninstall

```bash
pi remove npm:pi-open-tui
```

Removes the entry from `~/.pi/agent/settings.json`. Other packages and your open-tui config (`~/.pi/agent/open-tui.json`) are left untouched.

## Scope

pi-open-tui only changes TUI chrome — it does not alter model behavior, tool calls, or prompt contents.