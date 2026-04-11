---
name: dbay-kb-lint
description: Run the wiki lint or curate agent on a DBay KB, interpret the results, and walk the user through fixes. Use when the user wants to clean up the wiki, mentions too many pages, dead links, or invokes /dbay-kb-lint.
---

# /dbay-kb-lint

Run the lint / curate agent and interpret results.

## Decide: lint or curate

**Lint** (non-destructive) when the user wants to see issues without changes:
- Dead wikilinks
- Orphan pages (no incoming links)
- Pages violating the schema
- Missing citations

**Curate** (destructive) when the user wants the wiki compacted:
- Merge duplicate pages
- Delete generic / off-topic pages
- Split pages too broad
- Reorganize the index

Ask the user: "Lint (spot-only) or curate (merge/delete)?"

## Steps

1. **Baseline**: `wiki_list_pages()` — record current page count
2. **Trigger**: `wiki_lint(kb)` or `wiki_curate(kb)` — store task_id
3. **Poll** `wiki_task_status(task_id)` every ~15 seconds
4. **On completion**:
   - Fetch the final page list; compute delta vs baseline
   - For curate: report "{N} pages before → {M} after; {merged} merged, {deleted} deleted"
   - For lint: report "{K} issues found: {dead_links} dead links, {orphans} orphans, {format} format errors"
5. **If issues remain**, ask the user: "Want me to run curate now to fix them?"

## Rules

- Always baseline before trigger — otherwise you can't describe the delta
- For destructive curate, confirm with the user before trigger
- If agent returns `max_rounds_exceeded`, the wiki is too large for a single run — suggest running curate once per category (e.g. per tag)
