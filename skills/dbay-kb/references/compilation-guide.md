# DBay Wiki Page Compilation Guide

This reference expands the concise rules in `schema-template.md` with examples of what good wiki pages look like.

## Page structure

Every wiki page should start with:
1. A one-paragraph executive summary (1-3 sentences)
2. A `## 概述` or `## 背景` section
3. The substantive content
4. A `## 相关页面` section listing `[[wikilinks]]` to related pages
5. An optional `## 参考` section with citations

## Length targets

- 800-2500 Chinese characters is the sweet spot
- < 500 chars means the page is probably a fragment — consider folding into a parent page
- > 3000 chars means it should be split — propose `## Subtopic` → new wiki page via curate

## Wikilink density

A healthy page has **3-10** `[[wikilink]]` references scattered through the body. If a page has 0 wikilinks, it's probably an orphan. If it has 30+, it's probably just a list masquerading as an article.

## Title style

- Clear noun phrases: `数据库分片`, `KbWriteQueue 重试策略`
- Not too generic: avoid `概述`, `介绍`, `常见问题`
- Not too specific: `如何在 2026-04 修复 chunk drain bug` belongs in an issue tracker, not a wiki

## Citations

Use markdown footnotes for source traceability:

```markdown
本服务使用 Flyway 管理数据库迁移 [^1]。

[^1]: lakeon-api/CLAUDE.md, "Build & Test Commands"
```

## When to create vs update

| Situation | Action |
|---|---|
| Concept has a dedicated existing page | `update_page` that page |
| Concept is a section inside another page | `update_page` the parent, add `### Subsection` |
| Concept is genuinely new and central to the KB | `create_page` a new page |
| Concept is generic / well-known AI knowledge | Do NOT create; fold into an existing page if relevant at all |
