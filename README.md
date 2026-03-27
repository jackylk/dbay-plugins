# DBay — Claude Code Skills

Skills for integrating DBay memory and knowledge base into Claude Code.

## Skills

### dbay-memory
Cross-project long-term memory via `dbay` CLI. Triggers when user says "记住/remember" or asks about past decisions/context. Calls `dbay mem ingest`/`dbay mem recall` to persist memories across projects, sessions, and devices.

## Installation

```bash
/plugin marketplace add jackylk/dbay-plugins
/plugin install dbay-memory
```
