# graphify-opencode

OpenCode **V2** plugin that pairs [graphify](https://github.com/Graphify-Labs/graphify) with OpenCode: when a knowledge graph exists in the project, it reminds the agent to *query the graph* before brute-force grepping raw files.

On the first `bash` call of a session, if `graphify-out/graph.json` exists, the hook prepends a one-line reminder to the command so the agent sees:

> knowledge graph at graphify-out/. For focused questions, run graphify query with your question (scoped subgraph, usually much smaller than GRAPH_REPORT.md) instead of grepping raw files.

Only fires once per session, and only when the graph exists — zero noise otherwise.

## Install

Copy the plugin into your project (or `~/.opencode/plugins/` for global):

```bash
git clone https://github.com/Wesley-Source/graphify-opencode
cp graphify-opencode/graphify.js your-project/.opencode/plugins/graphify.js
```

Install the graphify CLI (which also ships the `/graphify` skill):

```bash
uv tool install graphifyy
```

Then build a graph in any project:

```bash
graphify .
```

## Notes

- Plain V2 API: `{ id, setup }` + `ctx.tool.hook("execute.before")` — no dependencies, no build step.
- The reminder string deliberately avoids backticks/`$(...)` (it's spliced into a double-quoted `echo`), and uses `;` instead of `&&` so PowerShell 5.1 doesn't choke on the first command of a session.
- Not affiliated with Graphify Labs — plugin is original code; `LICENSE`/`NOTICE` here cover graphify (Apache-2.0), which this tool wraps.
