# Install codegraph

> [codegraph](https://github.com/colbymchenry/codegraph) pre-indexes the codebase into a knowledge graph. The agent gets precise context (call chains, blast radius, related symbols) in one query — instead of crawling files one by one.

## Install codegraph

`cd` into the project directory, then install codegraph:

```bash
npx -y @colbymchenry/codegraph install
```

This command installs codegraph, but does not create the project's `.pi/mcp.json`. Wire up the MCP server manually as described below, then build the per-project graph:

```bash
npx -y @colbymchenry/codegraph init
```

All three steps are required: `install` installs codegraph, the `.pi/mcp.json` configuration connects it to pi, and `init` builds the index. Without `init`, the MCP tools have nothing to query.

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