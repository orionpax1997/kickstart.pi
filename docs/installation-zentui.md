# Install pi-zentui for pi

[pi-zentui](https://pi.dev/packages/pi-zentui) is a TUI styling extension for pi — a [Starship](https://starship.rs/)-inspired statusline footer (cwd, git branch & status, runtime version, context / tokens / cost) plus an [Opencode](https://opencode.ai/)-style bordered input box with model and provider shown inside the frame. Configure it from inside pi with `/zentui`.

## Install

```bash
pi install npm:pi-zentui
```

This adds the package to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the statusline and editor chrome are session-level UI — one install covers every project you open pi in, no per-project setup needed.

## Verify

In a fresh `pi` session, confirm both are visible:

- A **footer** at the bottom of the TUI showing your cwd, git branch & status, runtime, and context / token / cost indicators.
- A **bordered input box** with the model name and provider displayed inside the frame.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

In a pi session, run `/zentui` to open the interactive menu. It has five sections you can cycle with `Tab` / `Shift+Tab`:

- **Coloring** — color source and palette
- **Features** — toggle the custom editor, statusline, copy-friendly mode, fixed-editor
- **Layout** — arrangement of the TUI
- **Built-in segments** — show / hide individual footer segments (git status icons, user@host, time, etc.)
- **Extension segments** — placement for third-party extension statuses

You can also drive settings directly via slash-command shortcuts, e.g. `/zentui editor toggle`, `/zentui statusline toggle`, `/zentui copy-friendly toggle`.

User config is persisted at `~/.pi/agent/zentui.json`. Missing or invalid values fall back to Zentui defaults.

## Uninstall

```bash
pi remove npm:pi-zentui
```

Removes the entry from `~/.pi/agent/settings.json`. Other packages and your Zentui config (`~/.pi/agent/zentui.json`) are left untouched.

## Scope

pi-zentui only changes TUI chrome — it does not alter model behavior, tool calls, or prompt contents.