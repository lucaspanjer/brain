---
name: brain-ingest
description: Ingest a web page or article into the knowledge base. Use whenever the user shares a URL to learn from, says "ingest this", "add this to the brain", "read this article", "save this page", or pastes a link they want captured and compiled into the wiki. Also triggers for batch ingestion of multiple URLs. If the user drops a URL in conversation and seems to want it remembered or studied — this is the skill.
allowed-tools: Bash(agent-browser:*)
---

# Brain Ingest

Scrape a web page using the `agent-browser` CLI tool, save it as an immutable source in `raw/`, then compile the knowledge into wiki articles.

**Important**: Use the `agent-browser` CLI via Bash for all browser operations. Do NOT use Playwright MCP tools — they produce raw accessibility trees instead of clean text. The agent-browser CLI is purpose-built for content extraction.

## Prerequisites

This skill expects the knowledge base structure to exist (`raw/`, `wiki/index.md`, `wiki/log.md`, and subdirectories `wiki/concepts/`, `wiki/entities/`, `wiki/source-summaries/`, `wiki/comparisons/`). If it doesn't, tell the user to run brain-bootstrap first.

## Step 1: Scrape with agent-browser CLI

Open the URL, wait for load, and extract clean text — all via Bash:

```bash
agent-browser open <url> && agent-browser wait --load networkidle
```

Extract the page title and full body text. The `get text` command returns clean readable text, not HTML or accessibility trees:

```bash
agent-browser get title
agent-browser get text body > /tmp/page-content.txt
```

The body text is your primary content. Only use `snapshot -i` if you need to identify a specific content container to narrow extraction:

```bash
agent-browser snapshot -i
# Look for an article or main content element, e.g. @e18
agent-browser get text @e18 > /tmp/page-content.txt
```

For pages with lazy-loaded content, scroll and re-extract:

```bash
agent-browser scroll down 2000 && agent-browser wait 1500
agent-browser get text body > /tmp/page-content.txt
```

Close when done:

```bash
agent-browser close
```

### Content type patterns

- **Articles/blog posts**: Target the article body element. Strip nav, footer, cookie banners.
- **Documentation**: Capture code examples with their explanations.
- **Twitter/X posts**: Extract tweet text, author, and thread replies.
- **GitHub repos**: Get the README. Optionally note the repo structure.
- **PDFs**: agent-browser opens these directly. Extract visible text.

### Authentication

If the page requires login, use agent-browser's auth features:

```bash
# Reuse the user's existing Chrome session
agent-browser --profile Default open <url>

# Or a saved session
agent-browser --session-name <site> open <url>
```

## Step 2: Save to raw/

Create a markdown file with a slugified filename and provenance metadata.

**Filename**: `raw/YYYY-MM-DD-slugified-title.md`

```markdown
---
title: "Article Title"
source: https://example.com/article
ingested: YYYY-MM-DD
type: article
---

# Article Title

[extracted content, cleaned into readable markdown]
```

The `type` field: `article`, `paper`, `documentation`, `tweet`, `repo`, `video-transcript`, or `discussion`.

Clean up the text — remove nav, ads, cookie banners — but preserve the author's structure (headings, lists, code blocks). This is a faithful capture, not a rewrite.

## Step 3: Discuss with the user

Before compiling, briefly share:
- Title and source
- 2-3 key takeaways or novel concepts
- Which existing wiki articles might need updating (check `wiki/index.md`)

This keeps the user in the loop. They may want to steer emphasis or skip compilation for a particular source.

## Step 4: Compile into wiki

Read `wiki/index.md` to see what articles exist already.

Compile **all four article types** from each source — not just concepts and entities:

### Concepts and entities

For each concept or entity found in the source:

- **Article exists**: Integrate new information. Add the raw file to the `sources` list. Don't duplicate — merge.
- **No article**: Create one.

### Source summary

Create **one source summary per raw file** in `wiki/source-summaries/`. This is a structured overview of the entire source — what it covers, its key arguments, and its significance. Name it to match the raw file slug (e.g., `raw/2026-04-06-some-article.md` → `wiki/source-summaries/some-article.md`).

Source summaries are distinct from concepts: a concept extracts a single idea; a source summary captures the source as a whole — its thesis, structure, audience, and takeaways.

### Comparisons

Create a comparison article in `wiki/comparisons/` when:
- The source explicitly contrasts two or more approaches, tools, or ideas
- The new source presents an alternative perspective to something already in the wiki (e.g., a minimal vs. comprehensive take on the same topic)
- Two or more concepts in the wiki are frequently confused or have meaningful tradeoffs worth documenting

Name comparison files descriptively: `wiki/comparisons/rag-vs-wiki-compilation.md`, `wiki/comparisons/minimal-vs-full-agent-harnesses.md`.

Not every ingestion will produce a comparison — only create one when there's genuine contrast worth capturing.

### Article placement

Place each article in the subdirectory matching its type:
- `wiki/concepts/<slugified-topic>.md`
- `wiki/entities/<slugified-topic>.md`
- `wiki/source-summaries/<slugified-topic>.md`
- `wiki/comparisons/<slugified-topic>.md`

```markdown
---
title: Topic Name
type: concept | entity | source-summary | comparison
urls:
  - https://example.com/original-source
  - https://example.com/another-source
sources:
  - raw/YYYY-MM-DD-source-file.md
related:
  - other-topic
created: YYYY-MM-DD
updated: YYYY-MM-DD
confidence: high | medium | low
---

One-paragraph summary of this topic.

## Details

[Substantive content with clear headings]

## See Also

### Concepts
- [[related-concept]]

### Source Summaries
- [[related-source-summary]]
```

Group links by type. Omit empty groups. Every concept/entity article should link to its relevant source summaries, and every source summary should link back to the concepts/entities it covers.

The `urls` field preserves the original web URLs of primary sources that informed this article. This provides direct traceability from any wiki concept or entity back to the web. When updating an existing article with a new source, add its URL to the `urls` list.

### Inline citations

Use markdown footnotes to cite sources for key claims — specific numbers, quotes, non-obvious assertions, and attributed opinions. Don't cite every sentence; cite the ones a reader would want to verify.

```markdown
The model uses RMSNorm instead of LayerNorm and has 4,192 parameters[^1].

[^1]: https://karpathy.github.io/2026/02/12/microgpt/
```

Footnote labels should be short and sequential (`[^1]`, `[^2]`, ...). Place the footnote definitions at the bottom of the article, before the `## See Also` section. When an article draws from multiple sources, use different footnotes to distinguish which claim comes from where.

Confidence levels:
- **high** — Multiple corroborating sources or authoritative primary source
- **medium** — Single source, seems reliable
- **low** — Mentioned in passing, needs investigation

Cross-reference with `[[topic-name]]` wiki-links. Every article should link to related articles regardless of type, and those should link back.

## Step 5: Update index and log

**wiki/index.md** — Add one line per new article under the right category (`## Concepts`, `## Entities`, `## Source Summaries`, `## Comparisons`), using the subdirectory path:

```markdown
- [Topic Name](concepts/topic-name.md) — one-sentence description
- [Entity Name](entities/entity-name.md) — one-sentence description
- [Source Title](source-summaries/source-slug.md) — one-sentence description of the source
- [X vs Y](comparisons/x-vs-y.md) — one-sentence description of the comparison
```

**wiki/log.md** — Append:

```markdown
## [YYYY-MM-DD] ingest | Article Title
Source: raw/YYYY-MM-DD-slugified-title.md
URL: https://example.com/article
Pages created: topic-a.md, topic-b.md
Pages updated: existing-topic.md
Notes: What was novel in this source
```

## Batch Ingestion

When given multiple URLs, scrape all first, then compile holistically:

1. Scrape all URLs → save each to `raw/`
2. Read all new raw files together
3. Compile wiki updates with cross-source connections
4. Single index and log update covering all sources

This produces more coherent wiki articles than processing one at a time.
