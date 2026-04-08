---
name: brain-lint
description: Run a health check on the knowledge base wiki. Use when the user says "lint the brain", "check wiki health", "find gaps", "audit the knowledge base", or asks what's missing or inconsistent. Also suggest this after roughly every 10 ingestions to keep the wiki coherent as it grows.
---

# Brain Lint

A systematic health check that keeps the wiki coherent as it grows. Without periodic linting, wikis accumulate contradictions, orphan pages, and blind spots that degrade the value of the whole system.

## Workflow

### 1. Full scan

Read `wiki/index.md` and then every wiki article across all subdirectories (`wiki/concepts/`, `wiki/entities/`, `wiki/source-summaries/`, `wiki/comparisons/`). Build a mental map of:
- All articles, their types, and their directory placement
- All `[[wiki-links]]` — both outbound and inbound
- All `sources` and `urls` referenced in frontmatter
- All `related` connections

### 2. Check for issues

Flag each of these categories:

**Contradictions** — Two articles making conflicting claims. Quote the specific passages and which sources back each side. Don't resolve — present both and let the user decide.

**Orphan pages** — Articles with no inbound links from other articles. Every page should be reachable through the link graph. Suggest where links should be added.

**Missing backlinks** — Article A links to Article B, but B doesn't link back to A. Cross-references should be bidirectional across all article types.

**Ungrouped footers** — See Also sections that list links in a flat list instead of grouping by type. The correct format groups links under headings: Concepts, Entities, Source Summaries, Comparisons (omit empty groups).

**Stub concepts** — Topics mentioned in `[[wiki-links]]` that don't have their own article yet. List them with the articles that reference them.

**Unsourced claims** — Wiki articles with no entries in their `sources` frontmatter, or claims that go beyond what the listed sources actually say.

**Missing citations** — Articles with specific numbers, quotes, or non-obvious assertions that lack inline footnote citations. Every article should have at least a few `[^N]` footnotes on its key claims, linking to the source URL. Flag articles with zero footnotes as needing citations added.

**Stale content** — Claims superseded by newer sources. Check if newer raw files contain updated information that hasn't been compiled into the wiki yet.

**Index drift** — Articles that exist in `wiki/` subdirectories but aren't listed in `wiki/index.md`, or index entries pointing to nonexistent files.

**Misplaced articles** — Articles whose `type` frontmatter doesn't match their directory. A concept in `wiki/entities/` or an entity in `wiki/concepts/` should be moved. Also flag any articles still in the `wiki/` root that should be in a subdirectory.

**Missing URLs** — Wiki articles with `sources` but no `urls` in their frontmatter. Every article should trace back to original web sources where applicable. Check the corresponding raw file's `source` field to find the URL.

**Schema drift** — Read `AGENTS.md` and check it against the canonical template in `brain-bootstrap/SKILL.md`. Flag:
- Missing required sections: `What This Is`, `How It's Organized`, `Wiki Directory Structure`, `Special Files`, `Wiki Rules`, `Page Types`, `Operations`, `My Interests`
- Operations section missing the skill routing instruction ("All wiki operations are executed through brain skills") or missing any of the four skill references (`/brain-ingest`, `/brain-query`, `/brain-lint`, `/brain-bootstrap`)
- Wiki Rules missing any canonical rules from the template (frontmatter fields, wiki-link syntax, cross-references, contradiction handling, index updates, subdirectory placement, urls traceability)
- Page Types missing any of the four types or having types that don't match the wiki subdirectory structure
- Directory structure listing that doesn't match actual `wiki/` subdirectories

### 3. Present findings

Group issues by severity:
1. **Fix now** — Contradictions, index drift, stale content, schema drift
2. **Worth addressing** — Orphans, missing backlinks, unsourced claims
3. **Nice to have** — Stub concepts to investigate

For each issue, include enough context that the user can make a quick yes/no decision.

### 4. Fix approved issues

After the user reviews, fix what they approve:
- Add missing backlinks
- Convert ungrouped See Also sections to grouped format (Concepts, Entities, Source Summaries, Comparisons — omit empty groups)
- Add links from concept/entity articles to their relevant source summaries and comparisons
- Update index.md
- Create stub articles for missing concepts (in the correct subdirectory)
- Move misplaced articles to the correct subdirectory
- Add missing `urls` to frontmatter by looking up the raw file's `source` field
- Update stale content from newer sources
- Resolve contradictions per user direction
- Update AGENTS.md to match the canonical template (add missing sections, fix missing skill references, restore missing rules)

### 5. Log the lint

Append to `wiki/log.md`:

```markdown
## [YYYY-MM-DD] lint | Health check
Articles scanned: [count]
Issues found: [count by category]
Issues fixed: [list of what was resolved]
Remaining: [anything deferred]
```
