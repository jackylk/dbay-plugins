---
name: dbay-memory
description: "Use when the user says '记住/remember/save this' to store information, OR when the user asks about past decisions, conventions, credentials, or context that might have been discussed before. Calls dbay CLI to persist memories across projects, sessions, and devices. Also use proactively when you discover important information worth preserving (credentials, decisions, rejections, conventions)."
---

# DBay Memory — Cross-Project Long-Term Memory

You have access to the user's persistent memory via the `dbay` CLI. This memory survives across projects, sessions, and devices.

<HARD-GATE>
When the user says "记住/remember/save this", you MUST call `dbay mem ingest` via the Bash tool.
Do NOT rely solely on local/built-in memory files. DBay memory is the user's primary long-term store.
</HARD-GATE>

## When to Use

### Store (dbay mem ingest)
- User explicitly says "记住", "remember", "save this", "记到记忆库"
- You discover credentials, API keys, tokens
- A decision is made with rationale
- An approach is explicitly rejected
- A convention or workflow pattern is established
- A notable incident or debugging story occurs
- A step-by-step procedure is documented

### Recall (dbay mem recall)
- User asks "之前怎么做的", "what did we decide", "我的 API key 是什么"
- User asks about past decisions, conventions, or context
- Before starting a task, proactively check for relevant prior context
- When you need credentials or configuration that was previously stored

## Commands

### Store a memory
```bash
dbay mem ingest <base_id> "<content>" --type <memory_type> --importance <0.0-1.0>
```

**Memory types — choose the correct one:**
| Type | When to use | Example |
|------|-------------|---------|
| `fact` | Credentials, preferences, specs | "PyPI token is pypi-xxx" |
| `decision` | Choice + rationale | "chose asyncpg over psycopg2 for async support" |
| `rejection` | Approach rejected + why | "don't mock DB in integration tests — burned by mock/prod divergence" |
| `convention` | Team patterns, naming rules | "always use deploy.sh, never manual helm upgrade" |
| `procedural` | Step-by-step procedures | "deploy flow: build → push → deploy.sh" |
| `episode` | Incidents, debugging stories | "OOM caused by unbounded query cache, fixed with LRU eviction" |

**Importance guidelines:**
- `0.9` — Credentials, critical decisions, painful lessons
- `0.7` — Conventions, procedures
- `0.5` — General facts, preferences

### Recall memories
```bash
dbay mem recall <base_id> "<query>" [--type <filter>] [--top-k <n>]
```

### Get base_id
If you don't know the base_id, check `~/.dbay/config.json` for `memory_base`, or run:
```bash
dbay mem list
```

## Rules

1. **Extract MULTIPLE memories** from a single user message when appropriate.
   "We chose X because Y, and rejected Z because W" → one `decision` + one `rejection`.

2. **Always include context** in the memory content. Make it self-contained — it will be read months later in a different project.

3. **Never store ephemeral info** — current task progress, temporary state, things already in git history.

4. **Recall before asking** — If the user asks something that might have been discussed before, check memory first instead of asking them to repeat.
