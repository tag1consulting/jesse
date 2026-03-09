# Semantic Vault Search (QMD)

> **Optional recipe.** Your vault works without this. Enable it when your vault is large enough that searching beats reading.

[QMD](https://github.com/tobi/qmd) adds local semantic search to your vault — keyword matching, vector similarity, and hybrid re-ranking, all running on your machine. No cloud API, no data leaves your computer.

## Quick Setup

### 1. Install

Requires Node.js ≥ 22. On macOS, also `brew install sqlite`.

```bash
npm install -g @tobilu/qmd
```

### 2. Index your vault

```bash
qmd collection add /path/to/your/vault
qmd embed
```

First run downloads ~2 GB of GGUF models and indexes all `.md` files. Takes a few minutes on Apple Silicon. Subsequent runs are incremental.

### 3. Connect to your assistant

Add QMD as an MCP server. For Claude Code (`~/.claude/settings.json`) or Claude Desktop (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "qmd": {
      "command": "qmd",
      "args": ["mcp"]
    }
  }
}
```

Or install via plugin:

```bash
claude plugin marketplace add tobi/qmd
claude plugin install qmd@qmd
```

### 4. Add to your instruction file

In the `## Connected Tools` section:

```markdown
- **QMD (vault search)** -- Local semantic search over the vault. MCP
  tools: `query` (combined search), `get` (retrieve file by path or
  docid), `multi_get` (glob patterns), `status` (index health). Three
  query types: `lex` (BM25 keyword), `vec` (semantic), `hyde`
  (hypothetical answer passage). Use for discovery — direct reads
  for known paths.
```

### 5. Test it

```bash
qmd search "test query" -n 5     # Keyword (works immediately)
qmd vsearch "conceptual query"   # Semantic (needs embeddings)
qmd status                        # Check index health
```

## Keeping the Index Fresh

QMD doesn't auto-watch for changes. After vault edits:

```bash
qmd update && qmd embed
```

Both are incremental and fast.

## What You Get

Three search modes through a single `query` tool:

| Type | What it does | Good for |
|------|-------------|----------|
| `lex` | BM25 keyword search | Names, exact terms, file lookups |
| `vec` | Semantic vector search | "What did I decide about..." questions |
| `hyde` | Hypothetical document matching | Nuanced topics, complex queries |

Combine them in one query for best results. First sub-query gets 2× weight.

## System Requirements

| Requirement | Details |
|------------|---------|
| Node.js | ≥ 22 |
| macOS SQLite | `brew install sqlite` |
| Disk space | ~2 GB for GGUF models |
| Hardware | Apple Silicon strongly recommended |

Models: `embeddinggemma-300M` (~328 MB), `Qwen3-Reranker-0.6B` (~640 MB), `qmd-query-expansion-1.7B` (~1.1 GB). Stored in `~/.cache/qmd/models/`.

## Full Documentation

See the [Semantic Vault Search](https://tag1consulting.github.io/jesse/recipes/semantic-search.html) recipe for the complete guide including optional search strategy rules, collection context, and advanced configuration.

## PR Checklist

When submitting this recipe to the jesse repo:

- [ ] Add `semantic-search.md` to `docs/src/recipes/`
- [ ] Add `- [Semantic Vault Search](./recipes/semantic-search.md)` to `docs/src/SUMMARY.md` under Recipes
- [ ] Add this `README.md` to `template/Knowledge/Semantic-Search/` (or wherever the recipe's optional template files live)
- [ ] Verify with `mdbook serve --open` from the `docs/` directory
