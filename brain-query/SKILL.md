---
name: brain-query
description: Query the personal knowledge base wiki. Use when the user asks a question about topics in their brain, wants to look something up in the wiki, says "what do I know about X", "check the brain for Y", "search the wiki", or asks a question that their accumulated knowledge base might answer. If the user asks a factual or conceptual question that relates to their stated interests or previously ingested sources — check the wiki first.
---

# Brain Query

Answer questions by reading the wiki, not by relying on general knowledge. The wiki represents what the user has specifically chosen to learn and track — answers grounded in it are more relevant than generic responses.

## Workflow

### 1. Read the index

Start by reading `wiki/index.md` to find articles relevant to the question. The index is a compact catalog — scan it to identify which pages to read.

### 2. Read relevant articles

Read the wiki pages that seem relevant. Follow `[[wiki-links]]` and `related` frontmatter to discover connected articles. Cast a slightly wider net than the question literally asks — adjacent concepts often hold the answer.

### 3. Synthesize

Answer the question using information from the wiki articles. Cite sources with `[[wiki-links]]` so the user can dig deeper.

If the wiki has partial information, say what it covers and what's missing. Don't silently fill gaps with general knowledge — make it clear where the wiki's coverage ends and your general training begins.

### 4. Save valuable answers

If the answer is substantive and worth keeping, save it to `outputs/`:

**Filename**: `outputs/YYYY-MM-DD-slugified-question.md`

```markdown
---
question: "The user's question"
date: YYYY-MM-DD
sources:
  - wiki/concepts/relevant-article.md
urls:
  - https://example.com/original-source
---

[The synthesized answer]
```

Include `urls` from the wiki articles' frontmatter so the user can trace answers back to original web sources.

### 5. Compounding loop

If the answer reveals a concept that deserves its own wiki article but doesn't have one, offer to create it. This is how the wiki grows organically from usage — queries surface gaps, and filling them makes future queries richer.

### 6. Log the query

Append to `wiki/log.md`:

```markdown
## [YYYY-MM-DD] query | Short question summary
Question: The user's full question
Articles read: article-a.md, article-b.md
Output: outputs/YYYY-MM-DD-slugified-question.md (if saved)
Gaps identified: Any topics missing from the wiki
```
