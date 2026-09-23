# Install codegraph

> [codegraph](https://github.com/colbymchenry/codegraph) pre-indexes the codebase into a knowledge graph. The agent gets precise context (call chains, blast radius, related symbols) in one query — instead of crawling files one by one.

## Install codegraph

codegraph does not ship with a built-in pi agent target, so use the `opencode` target to generate the agent instructions, then clean up the leftover opencode config file:

```bash
npx -y @colbymchenry/codegraph install -t opencode -l local -y
```

This installs the codegraph binary and writes two files in the project directory:

- `AGENTS.md` — agent instructions for using codegraph (kept)
- `opencode.jsonc` — opencode MCP config (deleted below, since we use pi)

Delete `opencode.jsonc` immediately after install — it is only needed for opencode and is irrelevant to pi:

```bash
rm opencode.jsonc
```

Then build the per-project graph:

```bash
npx -y @colbymchenry/codegraph init
```

All steps are required: `install` installs codegraph and updates `AGENTS.md`, deleting `opencode.jsonc` removes the unused opencode config, the `.pi/mcp.json` entry below connects codegraph to pi, and `init` builds the index. Without `init`, the MCP tools have nothing to query.

## Configure MCP

Create `.pi/mcp.json` in the project root if it does not exist. If it already exists, merge the `codegraph` entry into its existing `mcpServers` object instead of replacing the file:

```json
{
  "mcpServers": {
    "codegraph": {
      "command": "codegraph",
      "args": ["serve", "--mcp"]
    }
  }
}
```

Restart pi. Verify with `/mcp` — `codegraph` should be listed.

## Verify

In a pi session:

> "How does function X reach function Y?"

pi should answer using the codegraph tools (you'll see it call `codegraph_explore`), returning call paths and source snippets rather than a series of `grep` results.

## Recommendation: install at the project level

- The graph index lives in `.codegraph/` — keeps it alongside the code it describes
- Unrelated projects stay unaffected
- Matches how codegraph is intended to be used (per-project, not per-machine)

## Scope

codegraph only exposes graph-query tools. It does not modify pi's other MCP servers, model selection, or theme. The index is regenerated from source — add `.codegraph/` to your project's `.gitignore`.