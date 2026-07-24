# Install OpenSpec

> [OpenSpec](https://github.com/Fission-AI/OpenSpec) is a spec-driven development tool for generating and managing project specifications. Pi is a first-class supported tool — pick it during `openspec init`.

## Prerequisites

```bash
which openspec || npm install -g @fission-ai/openspec@latest
```

## One-line init

`cd` into the project directory, then run:

```bash
openspec init --tools pi
```

This bootstraps OpenSpec in your project with pi tool integration. Skills land in `.pi/skills/openspec-*/` and slash commands in `.pi/prompts/opsx-*.md`.

For interactive tool selection (also works):

```bash
openspec init
```

…then choose `pi` from the list.

## Usage

After init, these slash commands are available in any pi session:

| Command | Purpose |
|---|---|
| `/opsx:propose` | Start a new change proposal |
| `/opsx:apply` | Apply a previously approved change |
| `/opsx:archive` | Archive a completed change |
| `/opsx:sync` | Sync specs to match code changes |

## Recommendation: install at the project level

- Specs are project-specific — global install adds unnecessary context to every project
- Keeps the agent focused on the specs that matter for the current project
- Matches the intended usage pattern (per-project, not per-machine)

## Avoid: global install

Global install loads spec tools into every session, cluttering the agent's context. Not recommended.

## Scope

OpenSpec only writes project-local files (`.pi/skills/openspec-*/SKILL.md` and `.pi/prompts/opsx-*.md`). It does not touch your global `~/.pi/agent/`, model settings, or other projects.