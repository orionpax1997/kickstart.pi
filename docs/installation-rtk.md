# Install rtk for pi

[rtk](https://github.com/rtk-ai/rtk) transparently rewrites verbose shell commands (`git status`, `pnpm list`, `vitest`, `cargo test`, …) into compact token-saving form. Each pi session is intercepted and rewritten automatically — same workflow, fewer tokens.

## Install

```bash
rtk init --agent pi --global
```

This creates `~/.pi/agent/extensions/rtk.ts`. Pi auto-discovers it on startup.

> Install at the **global** level: rtk is most useful when every project's pi session benefits from token-saved bash output. A single global install covers all work without per-project setup.

## Verify

```bash
rtk init --show
```

Then in a fresh `pi` session, run a chatty command like `git status` and confirm pi sees a shorter (rewritten) version instead of the raw output.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Uninstall

```bash
rtk init --uninstall --agent pi --global
```

Removes only the installed Pi extension file. Other files in `~/.pi/agent/extensions/` are untouched.

## Scope

rtk for pi currently only wraps the `bash` tool call. File reads (`read` / `grep` / `find` / `ls`), `write`, and `edit` are not rewritten.
