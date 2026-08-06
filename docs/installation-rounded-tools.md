# Install pi-rounded-tools for pi

[pi-rounded-tools](https://github.com/orionpax1997/pi-rounded-tools) swaps the square corners `┌┐└┘` on pi's built-in tools (`read`, `write`, `edit`, `bash`, `grep`, `find`, `ls`) for rounded ones `╭╮╰╯`. No extra shell, no theme matching of its own — border color just follows your pi theme's `border` token (yellow while running, red on failure, theme-default on success).

## Install

```bash
pi install npm:pi-rounded-tools
```

This adds the package to your global pi settings (`~/.pi/agent/settings.json`). Pi loads it on startup.

> Install at the **global** level: the rounded frames are session-level UI — one install covers every project you open pi in, no per-project setup needed.

## Verify

In a fresh `pi` session, run any built-in tool (`read`, `bash`, `edit`, …). The call and result blocks should be framed with rounded corners `╭ ╮ ╰ ╯` instead of square ones.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

There is no configuration. Border color is always derived from the active pi theme:

- **Running** — `warning` (yellow)
- **Failed** — `error` (red)
- **Succeeded** — `border` (theme default)

If you only want the rounded frame on a subset of tools (e.g. skip `bash`), edit the `tools` array at the bottom of `rounded-tools.ts` in the [extension source](https://github.com/orionpax1997/pi-rounded-tools) and reinstall.

## Uninstall

```bash
pi remove npm:pi-rounded-tools
```

Removes the entry from `~/.pi/agent/settings.json`. Other packages and your theme settings are left untouched.

## Conflicts

This extension **re-registers** the seven built-in tools above. It will conflict with any other extension that also overrides them — for example `pi-toolbox`, `pi-tool-display`, `pi-foldable-tools`, `pi-tidy-tools`. Install only one. If you want frames on MCP calls or sub-agents too, choose an alternative that wraps `ToolExecutionComponent` (e.g. `pi-toolbox`) instead of re-registering tools.

## Scope

pi-rounded-tools only changes the visual frame of tool output — tool execution, model behavior, and prompt contents are untouched.
