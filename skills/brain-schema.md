# Brain Schema

Canonical reference for wiki conventions shared across all brain skills. Skills reference this file instead of duplicating definitions.

## Directory Structure

```
raw/                    # Append-only source material
wiki/                   # LLM-maintained encyclopedia
  index.md              # Content catalog
  log.md                # Activity timeline
  concepts/             # Techniques, patterns, ideas
  entities/             # People, orgs, projects, tools
  source-summaries/     # Summaries of specific raw sources
  comparisons/          # Side-by-side analyses
outputs/                # Query answers and reports
```

## Page Types

| Type | Directory | Description |
|------|-----------|-------------|
| concept | `wiki/concepts/` | A technique, pattern, or idea |
| entity | `wiki/entities/` | A person, org, project, or tool |
| source-summary | `wiki/source-summaries/` | Summary of a specific raw source |
| comparison | `wiki/comparisons/` | Side-by-side analysis of related concepts or tools |

## Wiki Article Frontmatter

```yaml
title: Topic Name
type: concept | entity | source-summary | comparison
urls:
  - https://example.com/original-source
sources:
  - raw/YYYY-MM-DD-source-file.md
related:
  - other-topic
created: YYYY-MM-DD
updated: YYYY-MM-DD
confidence: high | medium | low
```

- `urls` — Original web URLs of primary sources. Provides traceability from any wiki article back to the web. When updating an article with a new source, add its URL to this list.
- `sources` — Raw files this article draws from.
- `confidence`:
  - **high** — Multiple corroborating sources or authoritative primary source
  - **medium** — Single source, seems reliable
  - **low** — Mentioned in passing, needs investigation

## Raw File Frontmatter

```yaml
title: "Article Title"
source: https://example.com/article
ingested: YYYY-MM-DD
type: article | paper | documentation | tweet | repo | video-transcript | discussion
```

**Filename**: `raw/YYYY-MM-DD-slugified-title.md`

## Article Body Structure

Every wiki article follows this structure:

1. One-paragraph summary (immediately after frontmatter)
2. `## Details` — Substantive content with clear headings
3. Inline footnote citations on key claims (before See Also)
4. `## See Also` — Cross-references grouped by type

## Inline Citations

Use markdown footnotes for key claims — specific numbers, quotes, non-obvious assertions, and attributed opinions. Don't cite every sentence; cite the ones a reader would want to verify.

```markdown
The model uses RMSNorm instead of LayerNorm and has 4,192 parameters[^1].

[^1]: https://karpathy.github.io/2026/02/12/microgpt/
```

Footnote labels: short and sequential (`[^1]`, `[^2]`, ...). Place definitions at the bottom of the article, before `## See Also`. Use different footnotes to distinguish which claim comes from which source.

## See Also Format

Group links by type under subheadings. Omit empty groups.

```markdown
## See Also

### Concepts
- [[related-concept]]

### Entities
- [[related-entity]]

### Source Summaries
- [[related-source-summary]]

### Comparisons
- [[related-comparison]]
```

Every concept/entity article should link to its relevant source summaries, and every source summary should link back to the concepts/entities it covers.

## Wiki-Link Syntax

Link related topics using `[[topic-name]]` wiki-link syntax. Cross-references are mandatory — pages link to related content, and those link back.

## Index Entry Format

One line per article under the matching category (`## Concepts`, `## Entities`, `## Source Summaries`, `## Comparisons`), using the subdirectory path:

```markdown
- [Topic Name](concepts/topic-name.md) — one-sentence description
- [Entity Name](entities/entity-name.md) — one-sentence description
- [Source Title](source-summaries/source-slug.md) — one-sentence description
- [X vs Y](comparisons/x-vs-y.md) — one-sentence description
```

## Wiki Rules Summary

- Every wiki file has YAML frontmatter (all fields from the template above)
- Every wiki file starts with a one-paragraph summary
- `[[wiki-links]]` for cross-references; bidirectional linking is mandatory
- See Also groups links by type (omit empty groups)
- When sources conflict, note the contradiction explicitly
- Keep `wiki/index.md` updated on every ingest
- Articles go in the subdirectory matching their type
