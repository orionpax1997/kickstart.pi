# Install superpowers

> [superpowers](https://github.com/obra/superpowers) provides a curated skill set for brainstorming, debugging, TDD, planning, code review, and more. Skills auto-load per task — no manual invocation once installed.

## One-line install

`cd` into the project directory, then run:

```bash
pi install https://github.com/obra/superpowers
```

This installs the skills + a small bootstrap extension into your pi session. Pi auto-discovers them on next startup.

## Optional: subagent support

A few superpowers workflows (delegated execution, code review) need a `subagent` tool. Pi core doesn't ship one, but [pi-subagents-lite](https://pi.dev/packages/pi-subagents-lite) fills the gap — see the [install guide](installation-subagents-lite.md):

```bash
pi install npm:pi-subagents-lite
```

Skip if you only plan to use the lighter skills (brainstorming, TDD, planning).

## Usage

Skills auto-load per task. pi decides which skill to invoke based on the user's request. You can also trigger explicitly with `/skill:<name>` once a session is running.

## Recommendation: install at the project level

- Skills are scoped to the project that benefits from them
- Keeps unrelated projects free of skill suggestions
- Matches how superpowers is intended (per-project, not per-machine)

## Avoid: global install

Global install loads superpowers on every session in every project, regardless of fit. Not recommended.

## Scope

Superpowers only changes **what pi knows** (skill content) and **how the bootstrap flows** (when skills are suggested). It does not modify pi's built-in tools, model selection, or theme.