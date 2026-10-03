# graphify-opencode

OpenCode **V2** plugin that pairs [graphify](https://github.com/Graphify-Labs/graphify) with OpenCode: when a knowledge graph exists in the project, it reminds the agent to *query the graph* before brute-force grepping raw files.

> **AI agents:** follow [Install](#install) exactly, in order.

## Install

**Step 1 — install the graphify CLI.**

```bash
uv tool install graphifyy
```

Fallbacks if `uv` is unavailable: `uvx graphifyy --help` (no install) or `pipx install graphifyy`.

**Step 2 — install the skill (per project, run inside the project root).**

```bash
graphify install --platform opencode
```

This writes `<project>/.opencode/skills/graphify/SKILL.md` — OpenCode V2 discovers it automatically.

**Step 3 — install this plugin (pick ONE scope).**

```bash
# per project (run inside the project root):
mkdir -p .opencode/plugins
git clone https://github.com/Wesley-Source/graphify-opencode /tmp/graphify-opencode
cp /tmp/graphify-opencode/graphify.js .opencode/plugins/graphify.js

# OR global (all projects):
mkdir -p ~/.opencode/plugins
git clone https://github.com/Wesley-Source/graphify-opencode /tmp/graphify-opencode
cp /tmp/graphify-opencode/graphify.js ~/.opencode/plugins/graphify.js
```

OpenCode V2 auto-discovers plugins in `.opencode/plugins/` / `~/.opencode/plugins/` — **no config edit needed**.

**Step 4 — verify.**

1. Build a graph in a project: `graphify .` → creates `graphify-out/graph.json`.
2. Restart OpenCode in that project and run any `bash` command.
3. The **first** bash call of the session gets a one-line `[graphify]` reminder prepended; later calls don't (fires once per session).
4. No `graphify-out/graph.json` → hook does nothing (by design).

## Alternative: upstream-managed install

The graphify CLI itself can wire OpenCode natively: `graphify opencode install` writes a graphify section to `AGENTS.md` plus its own hook plugin. This repo's `graphify.js` is the dependency-free local variant of that same idea — use one or the other, not both.

## How it works

On the first `bash` call of a session, if `graphify-out/graph.json` exists, the hook prepends:

> [graphify] knowledge graph at graphify-out/. For focused questions, run graphify query with your question (scoped subgraph, usually much smaller than GRAPH_REPORT.md) instead of grepping raw files.

- Plain V2 API: `{ id, setup }` + `ctx.tool.hook("execute.before")` — no dependencies, no build step.
- The reminder string deliberately avoids backticks/`$(...)` (it's spliced into a double-quoted `echo`), and uses `;` instead of `&&` so PowerShell 5.1 doesn't choke on the first command of a session.
- Not affiliated with Graphify Labs — plugin is original code; `LICENSE`/`NOTICE` here cover graphify (Apache-2.0), which this tool wraps.
