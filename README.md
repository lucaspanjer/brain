# Claude Brain Skills

Personal skills for managing a knowledge base wiki with Claude Code.

## Installation

Symlink skills to `~/.claude/skills/`:

```bash
ln -s ~/projects/claude-brain/agent-browser ~/.claude/skills/agent-browser
ln -s ~/projects/claude-brain/brain-bootstrap ~/.claude/skills/brain-bootstrap
ln -s ~/projects/claude-brain/brain-ingest ~/.claude/skills/brain-ingest
ln -s ~/projects/claude-brain/brain-lint ~/.claude/skills/brain-lint
ln -s ~/projects/claude-brain/brain-query ~/.claude/skills/brain-query
```

## Skills

### agent-browser

Browser automation CLI for AI agents. Use when interacting with websites, filling forms, clicking buttons, taking screenshots, extracting data, or automating any browser task.

### brain-bootstrap

Set up a new personal knowledge base from scratch. Use when starting a fresh brain project or reconfiguring scope and interests.

### brain-ingest

Ingest a web page or article into the knowledge base. Use when adding URLs, articles, or batch-importing sources into the wiki.

### brain-lint

Run a health check on the knowledge base wiki. Finds gaps, inconsistencies, and suggests improvements.

### brain-query

Query the personal knowledge base. Use when looking up topics, searching the wiki, or asking questions the knowledge base might answer.
