# Install codebase-memory-mcp

> [codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) is a single static binary that indexes the codebase into a persistent SQLite knowledge graph — 158 languages, 14 MCP tools, millisecond queries. Like codegraph, it gives the agent one call for structured context instead of file-by-file crawling.

## 1. Install the binary

```bash
npm i -g codebase-memory-mcp
```

The binary lands at `~/.local/bin/codebase-memory-mcp` (already on `PATH` after install). Verify:

```bash
codebase-memory-mcp --version
```

## 2. Wire the MCP server into the project

In the project root, create `.pi/mcp.json` if it does not exist. If it already exists, merge the `codebase-memory-mcp` entry into its existing `mcpServers` object instead of replacing the file. The `environment.CBM_CACHE_DIR` entry redirects the SQLite index to a project-local directory:

```json
{
  "mcpServers": {
    "codebase-memory-mcp": {
      "command": "codebase-memory-mcp",
      "args": [],
      "environment": {
        "CBM_CACHE_DIR": ".codebase-memory/cache"
      }
    }
  }
}
```

The relative path `.codebase-memory/cache` resolves against the project root when the MCP server starts there.

Restart pi. Verify with `/mcp` — `codebase-memory-mcp` should be listed.

## 3. Build the graph

From the project root, with the same `CBM_CACHE_DIR` so the index lands in the project-local directory:

```bash
CBM_CACHE_DIR=".codebase-memory/cache" codebase-memory-mcp cli index_repository '{"repo_path": "/absolute/path/to/project"}'
```

The first index call writes `.codebase-memory/cache/<project>.db`; subsequent searches query it directly. Without this step, the MCP tools have nothing to query.

## 4. Add the AGENTS.md priority block

codebase-memory-mcp exposes structural graph tools (`search_graph`, `trace_path`, `get_code_snippet`, `query_graph`, `get_architecture`, …). To make the agent prefer them over grep/glob, append this block to the project's `AGENTS.md`:

```markdown
<!-- codebase-memory-mcp:start -->
# Codebase Knowledge Graph (codebase-memory-mcp)

This project uses codebase-memory-mcp to maintain a knowledge graph of the codebase.
ALWAYS prefer MCP graph tools over grep/glob/file-search for code discovery.

## Priority Order
1. `search_graph` — find functions, classes, routes, variables by pattern
2. `trace_path` — trace who calls a function or what it calls
3. `get_code_snippet` — read specific function/class source code
4. `query_graph` — run Cypher queries for complex patterns
5. `get_architecture` — high-level project summary

## When to fall back to grep/glob
- Searching for string literals, error messages, config values
- Searching non-code files (Dockerfiles, shell scripts, configs)
- When MCP tools return insufficient results

## Examples
- Find a handler: `search_graph(name_pattern=".*OrderHandler.*")`
- Who calls it: `trace_path(function_name="OrderHandler", direction="inbound")`
- Read source: `get_code_snippet(qualified_name="pkg/orders.OrderHandler")`
<!-- codebase-memory-mcp:end -->
```

The `<!-- codebase-memory-mcp:start -->` / `<!-- codebase-memory-mcp:end -->` markers are anchor points so the block can be re-applied or removed cleanly later.

## 5. Ignore the cache directory

The SQLite index is regenerable from source — never commit it. Add to the project's `.gitignore`:

```gitignore
.codebase-memory/
```

## Recommendation: install at the project level

- The SQLite index and the project's `.pi/mcp.json` stay scoped to the project that benefits from them
- `CBM_CACHE_DIR` redirects the global cache to project-local — keeps the index alongside the code it describes
- Matches how codebase-memory-mcp is intended (per-project, not per-machine)