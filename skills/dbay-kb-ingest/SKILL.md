---
name: dbay-kb-ingest
description: Ingest a source (URL, file, or text) into a DBay knowledge base with human-in-the-loop approval. Reads the KB schema, asks socratic questions, then triggers the wiki agent. Use when the user explicitly wants to add a source to a DBay KB or invokes /dbay-kb-ingest.
---

# /dbay-kb-ingest

This is the ingest procedure of `dbay-kb` as a standalone command.

The user gives you a source: a URL, pasted text, or a file path. You walk them through compilation into the target KB.

## Steps

1. **Confirm the target KB** — if ambiguous, call `wiki_list_pages()` (returns the current default) and confirm with the user
2. **Read `wiki_get_schema`** — note the page budget, title rules, language
3. **Orient yourself** — `wiki_list_pages` for a compact overview; `wiki_search_pages` with keywords from the source
4. **Read the top 3-5 relevant pages** fully via `wiki_read_page`
5. **Summarize 3-5 takeaways** from the source, noting which existing pages it touches/contradicts
6. **Ask 2-5 socratic questions** (see main `dbay-kb` skill for examples)
7. **Wait for the user's answers**
8. **Upload the raw source** if it isn't already in the KB (use `knowledge_upload` / `knowledge_wiki_ingest`) — these return a `document_id`
9. **Call `wiki_ingest(document_id)`** — store the returned `task_id`
10. **Poll `wiki_task_status(task_id)`** every ~15 seconds until `completed` or `error`
11. **Report** the delta: pages created / updated, summary, any errors

## Rules

- Never call `create_page` directly (it isn't exposed to you)
- Never skip the schema read
- Never proceed past step 7 without user answers
- If task_status returns `max_rounds_exceeded`, warn the user and suggest running `wiki_curate` manually afterwards
