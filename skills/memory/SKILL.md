---
name: memory
description: "Use when the user says '记住/remember/save this' to store information, OR when the user asks about past decisions, conventions, credentials, or context that might have been discussed before. Persists memories across projects, sessions, and devices via dbay MCP. Also use proactively when you discover important information worth preserving (credentials, decisions, rejections, conventions)."
---

# DBay Memory — Cross-Project Long-Term Memory

You have access to the user's persistent memory via the dbay MCP tools. This memory survives across projects, sessions, and devices.

<HARD-GATE>
When the user says "记住/remember/save this", you MUST call the dbay MCP `memory_ingest` tool.
Do NOT rely solely on local/built-in memory files. DBay memory is the user's primary long-term store.
</HARD-GATE>

## When to Use

### Store (memory_ingest)
- User explicitly says "记住", "remember", "save this", "记到记忆库"
- You discover credentials, API keys, tokens
- A decision is made with rationale
- An approach is explicitly rejected
- A convention or workflow pattern is established
- A notable incident or debugging story occurs
- A step-by-step procedure is documented

### Recall (memory_recall)
- User asks "之前怎么做的", "what did we decide", "我的 API key 是什么"
- User asks about past decisions, conventions, or context
- Before starting a task, proactively check for relevant prior context
- When you need credentials or configuration that was previously stored

## MCP Tools

### Store a memory
Call `mcp__dbay__memory_ingest` with:
- `content`: The memory content — concise, structured, self-contained
- `memory_type`: One of the types below
- `importance`: 0.0-1.0

### Memory types — choose the correct one:
| Type | When to use | Example |
|------|-------------|---------|
| `fact` | Credentials, preferences, specs | "PyPI token is pypi-xxx" |
| `decision` | Choice + rationale | "chose asyncpg over psycopg2 for async support" |
| `rejection` | Approach rejected + why | "don't mock DB in integration tests — burned by mock/prod divergence" |
| `convention` | Team patterns, naming rules | "always use deploy.sh, never manual helm upgrade" |
| `procedural` | Step-by-step procedures | "deploy flow: build → push → deploy.sh" |
| `episode` | Incidents, debugging stories | "OOM caused by unbounded query cache, fixed with LRU eviction" |

### Importance guidelines:
- `0.9` — Credentials, critical decisions, painful lessons
- `0.7` — Conventions, procedures
- `0.5` — General facts, preferences

### Recall memories
Call `mcp__dbay__memory_recall` with:
- `query`: Natural language search query
- `memory_types`: Optional filter (list of types)
- `top_k`: Number of results (default 10)

### Browse and manage
- `mcp__dbay__memory_list` — Browse memories, filter by type
- `mcp__dbay__memory_delete` — Delete a specific memory by ID

## Rules

1. **Extract MULTIPLE memories** from a single user message when appropriate.
   "We chose X because Y, and rejected Z because W" → one `decision` + one `rejection`.

2. **Always include context** in the memory content. Make it self-contained — it will be read months later in a different project.

3. **Never store ephemeral info** — current task progress, temporary state, things already in git history.

4. **Recall before asking** — If the user asks something that might have been discussed before, check memory first instead of asking them to repeat.
