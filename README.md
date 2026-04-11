# DBay — Claude Code Skills

Skills for integrating DBay memory and knowledge base into Claude Code.

## Skills

### dbay-memory
Cross-project long-term memory via `dbay` CLI. Triggers when user says "记住/remember" or asks about past decisions/context. Calls `dbay mem ingest`/`dbay mem recall` to persist memories across projects, sessions, and devices.

### dbay-kb

Karpathy-style knowledge base compilation, query, and maintenance for DBay KBs. Orchestrates `wiki_ingest` / `wiki_curate` / `wiki_lint` agent runs through `dbay-mcp`'s MCP tools with human-in-the-loop approval at each checkpoint. Use when:
- Adding a new source to a KB
- Asking questions against a DBay KB's wiki
- Running lint/curate to clean up the wiki

Sub-skills (slash commands): `/dbay-kb-ingest`, `/dbay-kb-query`, `/dbay-kb-lint`.

## Installation

```bash
/plugin marketplace add jackylk/dbay-plugins
/plugin install dbay-memory
```
