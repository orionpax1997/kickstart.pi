# Install codegraph

> [codegraph](https://github.com/colbymchenry/codegraph) pre-indexes the codebase into a knowledge graph. The agent gets precise context (call chains, blast radius, related symbols) in one query — instead of crawling files one by one.

## One-line install

`cd` into the project directory, then run:

```bash
npx -y @colbymchenry/codegraph install
```

The installer auto-detects pi and wires up the MCP server. Then build the per-project graph:

```bash
npx -y @colbymchenry/codegraph init
```

Both steps are required — `install` connects the agent, `init` builds the index. Without `init`, the MCP tools have nothing to query.

## Manual MCP wiring (if installer doesn't detect pi)

Add to the project's `.mcp.json`:

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