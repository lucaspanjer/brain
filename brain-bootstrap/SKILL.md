---
name: brain-bootstrap
description: Set up a new personal knowledge base from scratch. Use when the user says "set up my brain", "create a knowledge base", "initialize the wiki", or when starting a fresh brain project in an empty or near-empty directory. Also use when the user wants to reconfigure their knowledge base scope or interests.
---

# Brain Bootstrap

## Overview

Interactive setup for a new personal knowledge base. Walks the user through defining what their brain is about, then scaffolds the directory structure and schema.

## Interview

Ask the user these questions one at a time. Don't dump them all at once — have a conversation.

1. **Name**: What should this knowledge base be called? (e.g., "AI Research Brain", "Startup Playbook", "Robotics Wiki"). This becomes the heading in AGENTS.md.
2. **Scope**: What domain or topic area does this cover? One sentence that defines the boundary of what belongs here and what doesn't. This helps during ingestion — content outside scope gets flagged rather than silently absorbed.
3. **Interests**: List the specific subtopics, themes, or questions you care most about. These act as a lens — when ingesting sources, extra attention goes to content touching these areas. Comma-separated or as a list.
4. **Existing material**: Do you have any files, bookmarks, or URLs you want to seed the brain with? If so, the user can follow up with `brain-ingest` after setup.

## Scaffold

Create the directory structure from [brain-schema.md](../brain-schema.md). Only create directories and files that don't already exist — never overwrite.

**wiki/index.md:**
```markdown
# [Brain Name] Index

## Concepts

## Entities

## Source Summaries

## Comparisons
```

**wiki/log.md:**
```markdown
# Activity Log
```

## Write AGENTS.md

Generate the schema file from the user's answers. This is the persistent instruction set that governs all brain operations across sessions.

```markdown
# [Brain Name]

## What This Is
A personal knowledge base about [scope].

## How It's Organized
- `raw/` — Append-only source material. Never modify these files after creation.
- `wiki/` — LLM-maintained encyclopedia, organized into subdirectories by page type.
- `outputs/` — Generated reports, query answers, and analyses.

## Wiki Directory Structure
wiki/
  index.md, log.md
  concepts/             — Techniques, patterns, ideas
  entities/             — People, orgs, projects, tools
  source-summaries/     — Summaries of specific raw sources
  comparisons/          — Side-by-side analyses

## Special Files
- `wiki/index.md` — Content catalog. One line per article, organized by category. Read this first to understand what exists.
- `wiki/log.md` — Append-only activity log. Every ingest, query, and lint gets an entry.

## Wiki Rules
- Every wiki file has YAML frontmatter (title, type, urls, sources, related, created, updated, confidence)
- The `urls` field preserves original web URLs for traceability to primary sources
- Every wiki file starts with a one-paragraph summary
- Link related topics using [[topic-name]] wiki-link syntax
- Cross-references are mandatory — pages link to related content, and those link back
- The See Also section groups links by type: Concepts, Entities, Source Summaries, Comparisons (omit empty groups)
- When sources conflict, note the contradiction explicitly rather than silently picking one
- Keep index.md updated on every ingest
- Articles go in the subdirectory matching their type

## Page Types
- **concept** → `wiki/concepts/` — A technique, pattern, or idea
- **entity** → `wiki/entities/` — A person, org, project, or tool
- **source-summary** → `wiki/source-summaries/` — Summary of a specific raw source
- **comparison** → `wiki/comparisons/` — Side-by-side analysis of related concepts or tools

## Operations

All wiki operations are executed through brain skills. Do not perform these operations ad-hoc — always use the corresponding skill.

- **Ingest** (`/brain-ingest`): Scrape URL → save to raw/ → compile wiki updates → update index and log
- **Query** (`/brain-query`): Read index → find relevant pages → synthesize answer → save to outputs/
- **Lint** (`/brain-lint`): Check for contradictions, orphan pages, missing backlinks, stale claims, concepts mentioned but unexplained
- **Bootstrap** (`/brain-bootstrap`): Set up a new knowledge base from scratch

## My Interests
[user's interest list]
```

Adapt the template to fit what the user told you — don't include sections that don't apply, and add any custom conventions they mentioned.

## Wrap Up

After scaffolding, confirm what was created and suggest next steps:
- "Drop a URL to ingest your first source" (triggers brain-ingest)
- "Ask a question about your wiki" (triggers brain-query)
- "Run a health check" (triggers brain-lint)
