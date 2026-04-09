---
name: lint
description: Run a health check on the knowledge base wiki. Use when the user says "lint the brain", "check wiki health", "find gaps", "audit the knowledge base", or asks what's missing or inconsistent. Also suggest this after roughly every 10 ingestions to keep the wiki coherent as it grows.
---

# Brain Lint

## Overview

A systematic health check that keeps the wiki coherent as it grows. Without periodic linting, wikis accumulate contradictions, orphan pages, and blind spots that degrade the value of the whole system.

## Workflow

### 1. Full scan

Read `wiki/index.md` and then every wiki article across all subdirectories (see [brain-schema.md](../brain-schema.md) for structure). Build a mental map of:
- All articles, their types, and their directory placement
- All `[[wiki-links]]` — both outbound and inbound
- All `sources` and `urls` referenced in frontmatter
- All `related` connections

### 2. Check for issues

Flag each of these categories:

**Contradictions** — Two articles making conflicting claims. Quote the specific passages and which sources back each side. Don't resolve — present both and let the user decide.

**Orphan pages** — Articles with no inbound links from other articles. Every page should be reachable through the link graph. Suggest where links should be added.

**Missing backlinks** — Article A links to Article B, but B doesn't link back to A. Cross-references should be bidirectional across all article types.

**Ungrouped footers** — See Also sections that don't match the grouped format in [brain-schema.md](../brain-schema.md).

**Stub concepts** — Topics mentioned in `[[wiki-links]]` that don't have their own article yet. List them with the articles that reference them.

**Unsourced claims** — Wiki articles with no entries in their `sources` frontmatter, or claims that go beyond what the listed sources actually say.

**Missing citations** — Articles with key claims that lack inline footnote citations per the citation format in [brain-schema.md](../brain-schema.md). Flag articles with zero footnotes.

**Stale content** — Claims superseded by newer sources. Check if newer raw files contain updated information that hasn't been compiled into the wiki yet.

**Index drift** — Articles that exist in `wiki/` subdirectories but aren't listed in `wiki/index.md`, or index entries pointing to nonexistent files.

**Misplaced articles** — Articles whose `type` frontmatter doesn't match their directory. A concept in `wiki/entities/` or an entity in `wiki/concepts/` should be moved. Also flag any articles still in the `wiki/` root that should be in a subdirectory.

**Missing URLs** — Wiki articles with `sources` but no `urls` in their frontmatter. Every article should trace back to original web sources where applicable. Check the corresponding raw file's `source` field to find the URL.

**Schema drift** — Read `AGENTS.md` and check it against the canonical template in `bootstrap/SKILL.md` and the conventions in [brain-schema.md](../brain-schema.md). Flag missing required sections, missing skill references, wiki rules that diverge from the schema, page types that don't match the directory structure, or directory listings that don't match actual `wiki/` subdirectories.

### 3. Present findings

Group issues by severity:
1. **Fix now** — Contradictions, index drift, stale content, schema drift
2. **Worth addressing** — Orphans, missing backlinks, unsourced claims
3. **Nice to have** — Stub concepts to investigate

For each issue, include enough context that the user can make a quick yes/no decision.

### 4. Fix approved issues

After the user reviews, fix what they approve:
- Add missing backlinks
- Convert ungrouped See Also sections to the grouped format in [brain-schema.md](../brain-schema.md)
- Add links from concept/entity articles to their relevant source summaries and comparisons
- Update index.md
- Create stub articles for missing concepts (in the correct subdirectory)
- Move misplaced articles to the correct subdirectory
- Add missing `urls` to frontmatter by looking up the raw file's `source` field
- Update stale content from newer sources
- Resolve contradictions per user direction
- Update AGENTS.md to match the canonical template and [brain-schema.md](../brain-schema.md) conventions

### 5. Log the lint

Append to `wiki/log.md`:

```markdown
## [YYYY-MM-DD] lint | Health check
Articles scanned: [count]
Issues found: [count by category]
Issues fixed: [list of what was resolved]
Remaining: [anything deferred]
```
