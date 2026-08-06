# Install pi-permission-system for pi

[@gotgenes/pi-permission-system](https://pi.dev/packages/@gotgenes/pi-permission-system) is a permission enforcement extension for pi — it gates every tool, bash, MCP, skill, and special operation against a single policy file. Three states (`allow` / `deny` / `ask`) and four layered surfaces (`path` → `external_directory` → per-tool patterns → `bash` patterns) cover most of what a coding agent can do, with UI confirmation dialogs for anything that isn't pre-approved.

> **Why pair it with `pi-subagents`?** When a sub-agent decides to `rm -rf` something, you want the same policy to apply. `@gotgenes/pi-permission-system` forwards `ask` prompts from non-UI child sessions back to the parent's prompt, so sub-agent operations are gated by the same rules. Install both at the global level and they cooperate out of the box.

## Install

```bash
pi install npm:@gotgenes/pi-permission-system
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the policy file is `~/.pi/agent/extensions/pi-permission-system/config.json` and it applies to every session. Project-level overrides live in `<cwd>/.pi/extensions/pi-permission-system/config.json` and only tighten global rules (they cannot relax a global `deny`).

## Configure

Create the global policy file at `~/.pi/agent/extensions/pi-permission-system/config.json`:

```jsonc
{
  "permission": {
    "*": "allow",
    "path": {
      "*": "allow",
      "*.env": "deny",
      "*.env.*": "deny",
      "*.env.example": "allow"
    },
    "bash": {
      "*": "ask",
      "rm -rf *": "deny",
      "sudo *": "ask"
    },
    "external_directory": "ask"
  }
}
```

Then restart pi, or run `/reload` inside an existing session.

### What each surface does

| Surface | Purpose |
| --- | --- |
| `path` | Cross-cutting gate for **all** file access (tools + bash + MCP + extensions). Right place for `.env`, `~/.ssh/*`. |
| `external_directory` | CWD-boundary gate — prompts before file tools or bash reach outside the working tree. Accepts a pattern map so specific outside-CWD dirs (e.g. `~/.cargo/registry`) can be `allow`-listed. |
| `bash` | Command pattern matching with `*` wildcards. Last matching rule wins. |
| `*` | Coarse fallback for everything not covered above. |

The four layers compose **most-restrictive-wins**: a `path: "deny"` cannot be loosened by a per-tool `allow`, and an `external_directory: "ask"` cannot be loosened by `path: "allow"`. See the [Configuration reference](https://github.com/gotgenes/pi-permission-system/blob/main/docs/configuration.md) for the full surface list and merge semantics.

### Permission states

| State | Behavior |
| --- | --- |
| `allow` | Permits the action silently. |
| `deny` | Blocks the action with an error message returned to the LLM. |
| `ask` | Pops a UI dialog. You can approve once or approve a pattern for the rest of the session (see [session approvals](https://github.com/gotgenes/pi-permission-system/blob/main/docs/session-approvals.md)). |

## Verify

In a fresh `pi` session:

1. Ask the agent to `cat ~/.ssh/id_ed25519` — it should block with a `deny` from the `path` surface.
2. Ask the agent to read `../some-other-project/README.md` — it should pop an `external_directory: ask` dialog (since it leaves the cwd).
3. Ask the agent to run `rm -rf node_modules` — it should block from `bash: "rm -rf *": "deny"`.

If any of these silently succeed, your config file isn't being read — double-check the path (`~/.pi/agent/extensions/pi-permission-system/config.json`) and that JSON parses (`jq . ~/.pi/agent/extensions/pi-permission-system/config.json`).

## Customize

- **Project overrides** — drop a `config.json` into `<cwd>/.pi/extensions/pi-permission-system/`. The extension loads project config only when the directory is trusted, so an untrusted repo cannot loosen your global policy.
- **Per-agent overrides** — add YAML frontmatter to a global agent definition (`~/.pi/agent/agents/<name>.md`) for role-specific policies (e.g. stricter bash rules on the `explore` agent).
- **Patterns** — `*` is the only wildcard. Use the [pattern suggester](https://github.com/gotgenes/pi-permission-system/blob/main/docs/session-approvals.md#pattern-suggestions) by approving a similar prompt once and the dialog will offer a generated rule.

## Uninstall

```bash
pi remove npm:@gotgenes/pi-permission-system
```

Removes the entry from `~/.pi/agent/settings.json`. **Note**: your policy file at `~/.pi/agent/extensions/pi-permission-system/config.json` is left untouched — delete it manually if you want a fully fresh start:

```bash
rm -rf ~/.pi/agent/extensions/pi-permission-system
```

## Scope

`@gotgenes/pi-permission-system` adds the permission gate, hides disallowed tools before the agent starts, and forwards `ask` prompts from non-UI child sessions (sub-agents) back to the parent's UI. It does **not** change pi's main agent behavior, system prompt, default tool list, model selection, or session lifecycle — when the gate is removed, pi behaves exactly as before.
