---
name: dbay-kb
description: Use when working with a DBay knowledge base — ingesting new sources, asking questions against the wiki, or running lint/curate. Orchestrates the Karpathy-style compile → query → lint cycle via dbay-mcp's wiki_* tools with human-in-the-loop approval at each checkpoint. Do NOT use for general markdown editing or non-DBay wikis.
---

# DBay Knowledge Base Skill

This skill helps you interact with a DBay knowledge base the Karpathy way: the LLM compiles sources into an interlinked wiki, you answer questions against that wiki, and the wiki grows smarter over time.

Each KB has a `/schema.md` document (seeded on KB creation, co-evolved by the agent) that tells the wiki agent how to maintain the KB. **Always read it first** at the start of any session.

## When to apply

- User gives you a source (URL, file path, pasted text) to add to a DBay KB → run **ingest** procedure (or invoke `/dbay-kb-ingest`)
- User asks a question that should be answered from an existing DBay KB → run **query** procedure (or `/dbay-kb-query`)
- User says "clean up the wiki" / "there are too many pages" / "check for dead links" → run **lint/curate** procedure (or `/dbay-kb-lint`)
- User wants to see how the wiki is organized → call `wiki_list_pages` and `wiki_get_schema`

## Prerequisites

- `dbay-mcp` MCP server configured and connected. Verify by calling `wiki_list_pages` with no args — should return a page list or a clear "no KB selected" message.
- User has at least one KB. If not, ask them to create one in the Console first (or via the `knowledge_upload` tool which auto-creates a default KB).

## Procedures

### Procedure 1: Ingest a new source (human-in-the-loop)

1. **Identify the source type**: URL, local file, or pasted text
2. **Read the schema** with `wiki_get_schema` — note the page budget, language, and scope rules
3. **List existing pages** with `wiki_list_pages`; present a compact overview to the user
4. **Search for related pages** with `wiki_search_pages` using 2-3 key terms from the source
5. **Read the 3-5 most relevant existing pages** with `wiki_read_page` so you understand the current state
6. **Summarize takeaways** — produce a 3-5 bullet summary of what the source adds/contradicts
7. **Ask 2-5 socratic questions** — not "what should I do?" but "I think X — correct?":
   - "The source describes approach A; the wiki's `[[Page Y]]` describes approach B. Is A an evolution or an alternative?"
   - "This source mentions `<concept Z>` which has no wiki page yet. Should I propose a new one or fold it into `[[Existing Page]]`?"
   - "Page budget: schema says max 3 new pages per ingest. I see 5 candidate new concepts — which 3 matter most?"
8. **Wait for the user to answer**. Do not proceed until you have their input.
9. **Upload the source** if it isn't already in the KB. Use `knowledge_upload` (for files) or `knowledge_wiki_ingest` (for text) — NOT the agent. These tools return a `document_id`.
10. **Trigger the agent** with `wiki_ingest(document_id, kb_name_or_id)` — returns `task_id`
11. **Poll** with `wiki_task_status(task_id)` every ~15 seconds until status is `completed` or `error`
12. **Report the delta**: which pages were created/updated, summarize the agent's log_note entry

### Procedure 2: Query the wiki

Phase A — answer from the wiki:

1. **Read the schema** with `wiki_get_schema`
2. **Find relevant pages** via `wiki_search_pages` (start with 2-3 keyword variants)
3. **Read the top 3-5 matches** fully with `wiki_read_page`
4. **Synthesize**: every factual claim in your answer traces back to a `[[Wiki Page Title]]` citation
5. **Match format to question type**:
   - Factual → prose with inline wikilink citations
   - Comparison → table with rows per alternative
   - How-it-works → numbered steps
   - "What do we know about X" → structured summary with **Known** / **Open questions** / **Gaps**
6. **Flag gaps explicitly**: "The wiki has no page covering X" or "`[[Page Y]]` doesn't yet address Z"

Phase B — file it back:

7. **If the answer is durable** (comparison table, trade-off analysis, new synthesis), ask the user: "Should I promote this to a wiki page? It would become `[[Title]]`."
8. **If user agrees**: upload the synthesis as text via `knowledge_wiki_ingest`, then `wiki_ingest(document_id)` to compile it into the wiki.

### Procedure 3: Lint and curate

1. **List all pages** with `wiki_list_pages`; if total page count seems high for the domain (rule of thumb: > 3× source doc count), flag it to the user
2. **Ask the user's intent**: "Lint (spot issues only) or curate (merge/delete)?" — the two trigger different agent behaviors
3. **Trigger**: `wiki_lint(kb)` or `wiki_curate(kb)`, get task_id
4. **Poll** with `wiki_task_status`
5. **On completion**, summarize: "Agent merged N pages, deleted M, flagged K dead wikilinks"
6. **For destructive curate**, before the user's next ingest, run `wiki_list_pages` again and present a before/after diff (page count, new titles, removed titles)

## Anti-patterns to avoid

- **Answering from general knowledge** — always read the wiki first. Contradictions with your priors are signal, not error.
- **Creating pages directly** — dbay-mcp does not expose `create_page` to you; always go through `wiki_ingest` (so the agent enforces schema rules).
- **Skipping the schema read** — schemas evolve; yesterday's rules may not apply.
- **Proceeding without user answers** to socratic questions.
- **Long-running polls** without telling the user the task_id and expected duration.

## Related skills

- `dbay-mem` — long-term memory; useful to store the user's answers to socratic questions so future ingest sessions inherit context
- `/dbay-kb-ingest`, `/dbay-kb-query`, `/dbay-kb-lint` — focused sub-skills invoked as slash commands
