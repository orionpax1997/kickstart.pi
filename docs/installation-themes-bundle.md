# Install @firstpick/pi-themes-bundle for pi

[@firstpick/pi-themes-bundle](https://pi.dev/packages/@firstpick/pi-themes-bundle) adds sixteen custom themes to pi's theme discovery — dark and light terminal palettes based on Catppuccin, Dracula, Tokyo Night, Gruvbox, Nord, Rosé Pine, One Dark, Solarized, and Everforest, plus Matrix-inspired and dark crimson custom palettes.

## Install

```bash
pi install npm:@firstpick/pi-themes-bundle
```

This adds the package to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers the themes on startup.

> Install at the **global** level: themes are a session-level cosmetic preference — one install covers every project you open pi in, no per-project setup needed.

## Verify

In a fresh `pi` session, run `/settings` and confirm the **Theme** picker lists the bundled entries (`catppuccin-mocha`, `dracula`, `tokyo-night`, `gruvbox-dark`, `nord`, `rose-pine`, `one-dark`, `solarized-dark`, `everforest-dark`, `matrix`, `crimson-noir`, …).

## Activate

Pick a theme in `/settings`, or set it directly in `~/.pi/agent/settings.json`:

```json
{
  "theme": "tokyo-night"
}
```

Restart pi, or run `/reload` inside an existing pi session.

## Included themes

| | | |
|---|---|---|
| `catppuccin-latte` | `catppuccin-mocha` | `crimson-noir` |
| `dracula` | `everforest-dark` | `gruvbox-dark` |
| `gruvbox-light` | `matrix` | `nord` |
| `one-dark` | `rose-pine` | `rose-pine-dawn` |
| `solarized-dark` | `solarized-light` | `tokyo-night` |
| `tokyo-night-storm` | | |

## Uninstall

```bash
pi remove npm:@firstpick/pi-themes-bundle
```

Removes the entry from `~/.pi/agent/settings.json`. Your selected theme is left intact — pi falls back to its built-in default if the removed bundle was the source.

## Scope

pi-themes-bundle only contributes palette files — it does not alter model behavior, tool calls, prompt contents, or TUI chrome.