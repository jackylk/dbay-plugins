---
name: dbay-kb-query
description: Answer a question against a DBay KB's wiki using only wiki content (not general knowledge), then optionally promote durable answers back to the wiki. Use when the user asks a question that should be grounded in a DBay KB or invokes /dbay-kb-query.
---

# /dbay-kb-query

Answer the user's question from the wiki, then optionally file the answer back.

## Phase A — Answer from the wiki

1. **Read `wiki_get_schema`** to understand the KB's scope and conventions
2. **Find relevant pages** with `wiki_search_pages` — try 2-3 keyword variants
3. **Read the top 3-5 matches** fully with `wiki_read_page`
4. **Follow one hop** of interesting `[[wikilinks]]` — stop at one hop, don't recursively expand
5. **Synthesize** the answer grounded only in wiki content. Cite every claim with `[[Page Title]]`
6. **Match format to question type**:
   - Factual → prose with inline citations
   - Comparison → table
   - How-it-works → numbered steps
   - What-do-we-know → Known / Open questions / Gaps
7. **Flag gaps** explicitly: "The wiki has no page on X" or "`[[Page Y]]` doesn't cover Z"

## Phase B — File it back (optional)

8. **Ask the user**: "This answer is durable. Should I promote it to a wiki page?"
9. **If yes**: upload as text via `knowledge_wiki_ingest`, then `wiki_ingest(document_id)`.

## Rules

- **Do NOT** answer from general knowledge. Read the wiki first. Contradictions are signal.
- **Do NOT** create pages directly.
- If `wiki_search_pages` returns no hits on 3 different keyword variants, tell the user: "The wiki has no coverage of this topic. Do you want to add a source?"
