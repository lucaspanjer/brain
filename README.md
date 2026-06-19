# Brain

A Claude Code plugin for managing a personal knowledge base wiki. See [karpathy/llm-wiki.md](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) for the inspiration.

## Installation

**From marketplace:**

```
/plugin marketplace add lucaspanjer/brain-marketplace
/plugin install brain@brain-marketplace
```

**For local development:**

```bash
claude --plugin-dir /path/to/brain
```

## Skills

| Skill | Description |
|-------|-------------|
| `/brain:bootstrap` | Set up a new knowledge base from scratch |
| `/brain:ingest` | Ingest a web page or article into the wiki |
| `/brain:query` | Query the knowledge base for answers |
| `/brain:lint` | Run a health check on wiki consistency |
| `/brain:agent-browser` | Browser automation for web scraping |
