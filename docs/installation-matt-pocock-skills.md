# Install mattpocock/skills

> [mattpocock/skills](https://github.com/mattpocock/skills) is a curated set of engineering skills ("Skills for Real Engineers") by Matt Pocock — grilling interviews, TDD, debugging, code review, architecture surveys, triage, and more. Skills auto-load per task; a few also register as `/skill:<name>` commands.

## One-line install

`cd` into the project directory, then run:

```bash
npx skills@latest add mattpocock/skills --agent pi -y
```

The installer fetches the repo, lets you pick which skills to copy, and writes them to `.pi/skills/<name>/` in your project. It also drops a copy under `.agents/skills/` so other Agent-Skills-spec agents can see them.

### If you'd rather pick interactively (no `-y`)

```bash
npx skills@latest add mattpocock/skills --agent pi
```

Then select the skills you want from the multi-select prompt.

### Common flags

| Flag | Purpose |
|---|---|
| `--agent pi` | Target pi (writes to `.pi/skills/`). Omit to install for every supported agent. |
| `--skill <name>` | Install one specific skill. Repeat to install several. Use `'*'` for all. |
| `-y, --yes` | Skip prompts (non-interactive). |
| `--list` | List the skills in the repo without installing. |

Examples:

```bash
# Browse what's available before committing
npx skills@latest add mattpocock/skills --list

# Install only the setup wizard + a handful of day-to-day skills
npx skills@latest add mattpocock/skills --agent pi --skill \
  setup-matt-pocock-skills \
  grill-me \
  tdd \
  diagnosing-bugs \
  triage

# Install everything
npx skills@latest add mattpocock/skills --agent pi --skill '*' -y
```

## One-time setup per repo

After installing, run `/skill:setup-matt-pocock-skills` **once per project**. It asks three questions and writes config files into the repo:

1. **Which issue tracker?** GitHub, GitLab, Linear, or local files
2. **What triage labels do you use?** (the `/triage` skill needs them)
3. **Where do domain docs (`CONTEXT.md`, ADRs) live?**

Several other skills (e.g. `/triage`, `/to-spec`, `/to-tickets`, `/grill-with-docs`) read this config, so the setup wizard is effectively a prerequisite — pick it during install.

## What you get

The repo splits skills into **user-invoked** (only reachable when you type them, e.g. `/skill:grill-me` — they orchestrate a workflow) and **model-invoked** (auto-loadable when the task fits — they hold the reusable discipline).

Highlights:

| Skill | Trigger | What it does |
|---|---|---|
| `grill-me` | `/skill:grill-me` | Relentless interview to sharpen a plan or design before any code is written |
| `grill-with-docs` | `/skill:grill-with-docs` | Same grilling, but also builds `CONTEXT.md` and ADRs as it goes |
| `tdd` | auto + `/skill:tdd` | Red-green-refactor loop for features and bug fixes |
| `diagnosing-bugs` | auto + `/skill:diagnosing-bugs` | Disciplined, phase-gated debugging loop |
| `triage` | auto + `/skill:triage` | Move issues and external PRs through a triage state machine |
| `code-review` | auto + `/skill:code-review` | Review the changes since a fixed point along Standards + Spec axes |
| `implement` | auto + `/skill:implement` | Implement a piece of work from a spec or set of tickets |
| `to-spec` | auto + `/skill:to-spec` | Turn the conversation into a spec published to the issue tracker |
| `to-tickets` | auto + `/skill:to-tickets` | Break a plan into tracer-bullet tickets with explicit blocking edges |
| `wayfinder` | auto + `/skill:wayfinder` | Plan a huge chunk of work as a map of decision tickets |
| `improve-codebase-architecture` | `/skill:improve-codebase-architecture` | Survey the codebase for "deepening" opportunities and hand you the candidates |
| `codebase-design` | auto + `/skill:codebase-design` | Shared vocabulary for designing deep modules |
| `domain-modeling` | auto + `/skill:domain-modeling` | Pin down terminology and ubiquitous language |
| `research` | auto + `/skill:research` | Investigate a question against primary sources, capture as Markdown |
| `prototype` | auto + `/skill:prototype` | Build a throwaway prototype to answer a design question |
| `resolving-merge-conflicts` | auto + `/skill:resolving-merge-conflicts` | Walk through an in-progress merge/rebase conflict |
| `wizard` | auto + `/skill:wizard` | Generate an interactive bash wizard for steps only a human can do |

The full list is at [skills.sh/mattpocock/skills](https://skills.sh/mattpocock/skills).

## Recommendation: install at the project level

- Skills are scoped to the workflow of a specific repo (TDD on this codebase, triage of this repo's issues, architecture of this design)
- The setup wizard writes **per-repo config** (issue tracker choice, label vocabulary, doc paths) — global install can't really make sense of that
- Skills land under `.pi/skills/`, which is committed-friendly — you can review the diff
- `CONTEXT.md` / `CONTEXT/*.md` / `ADR-*.md` artifacts are project-local files anyway
- Keeps unrelated projects free of skill suggestions

## Avoid: global install

Global install (drop `--agent pi` and don't pass `-g` to avoid project-level; or pass `-g` for `~/`) loads every skill into every session in every project, regardless of fit. Not recommended — the surface area is huge, and the setup wizard's per-repo config doesn't survive globally.

## Updating

The repo moves fast. Pull new versions with:

```bash
npx skills@latest update
```

This updates skills installed at the current scope (project if you're in one, else global) and is non-interactive with `-y`.

## Scope

mattpocock/skills only writes project-local files:

- `.pi/skills/<name>/SKILL.md` (and bundled assets)
- `.agents/skills/<name>/` (mirror copy)
- After `/skill:setup-matt-pocock-skills`: `.agents/<repo>/` config files (tracker choice, labels, doc layout)

It does not touch your global `~/.pi/agent/`, model settings, theme, or other projects. Uninstall with `npx skills@latest remove <name>`.
