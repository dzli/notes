# Wiki Schema

This is a personal knowledge base for a software developer, focused on software engineering, AI, LLMs, and agents. Articles are web-clipped to markdown and stored in `raw/`. The LLM maintains the `wiki/` layer entirely.

## Directory structure

```
raw/           ← immutable web-clipped source articles (never modify)
  assets/      ← downloaded images
wiki/          ← LLM-maintained knowledge base
  index.md     ← catalog of all wiki pages (update on every ingest)
  log.md       ← append-only operation log
  taxonomy.md  ← hierarchical tag vocabulary (extend as needed)
  tags/        ← one page per tag
  sources/     ← one summary page per ingested article
  concepts/    ← concept/topic synthesis pages (created as needed)
CLAUDE.md      ← this file
```

## Tag system

- Tags are hierarchical. See `wiki/taxonomy.md` for the full tree.
- An article tagged `claude` implicitly inherits `llm` and `ai`.
- When searching for a tag, always include all descendants.
- On ingest, assign tags from the existing taxonomy first. Add new nodes only when genuinely needed, placing them in the correct parent.
- Tags use lowercase kebab-case.

## Ingest workflow

When the user says "ingest [filename]" or drops a file in `raw/`:

1. Read the source file from `raw/`
2. Briefly discuss key takeaways with the user (2-4 bullet points)
3. Create `wiki/sources/[slug].md` with: title, source URL if present, date ingested, summary (3-5 paragraphs), key points, and assigned tags
4. For each assigned tag:
   - If `wiki/tags/[tag].md` doesn't exist, create it
   - Add this source to the tag page
   - Ensure parent tags also reference this source
5. Update or create any relevant `wiki/concepts/` pages touched by this source
6. Update `wiki/index.md` — add the source and any new tag/concept pages
7. Append to `wiki/log.md`: `## [YYYY-MM-DD] ingest | [article title]`

### Source page format

```markdown
---
title: [Article Title]
url: [original URL if available]
date: [YYYY-MM-DD]
tags: [tag1, tag2, tag3]
---

# [Article Title]

## Summary
[3-5 paragraph summary]

## Key Points
- ...

## Related
- [link to relevant wiki pages]
```

### Tag page format

```markdown
---
tag: [tag-name]
parent: [parent-tag]
children: [child1, child2]
---

# [Tag Name]

[1-2 sentence description of this concept]

## Articles
- [[sources/slug]] — one-line description

## Related Tags
- [[tags/related-tag]]
```

## Query workflow

When the user asks a question:

1. Read `wiki/index.md` to identify relevant pages
2. If the question involves a tag, read `wiki/taxonomy.md` and expand to all descendants
3. Read the relevant source and concept pages
4. Synthesize an answer with citations (link to wiki pages, not raw sources)
5. If the answer is non-trivial and reusable, offer to save it as a new `wiki/concepts/` page

## Lint workflow

When the user says "lint":

1. Scan all wiki pages for: broken links, orphan pages (no inbound links), contradictions between pages, stale claims, concepts mentioned but lacking their own page
2. Report findings as a checklist
3. Ask which issues to fix

## Conventions

- All wiki files use lowercase kebab-case filenames
- Dates use YYYY-MM-DD format
- Internal links use Obsidian `[[wiki-link]]` format
- Never modify files in `raw/`
- Keep `index.md` and `log.md` up to date on every operation
