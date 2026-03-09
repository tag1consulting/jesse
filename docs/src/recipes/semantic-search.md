# Semantic Vault Search

Add local semantic search to your vault so your assistant can find things by meaning, not just keywords. This recipe uses [QMD](https://github.com/tobi/qmd), a local search engine that combines BM25 full-text search with vector embeddings and LLM-powered re-ranking — all running on your machine via small GGUF models. No cloud API, no data leaves your computer.

> **This recipe is optional.** It requires local software installation and ~2 GB of disk space for search models. Your vault works fine without it — the assistant will use file reads and directory scanning instead. Enable this when your vault grows large enough that searching is faster than reading.

## Prerequisites

- **Node.js ≥ 22** — check with `node --version`
- **macOS:** Homebrew SQLite — `brew install sqlite`
- **~2 GB disk space** for three GGUF models (downloaded automatically on first use)
- **Apple Silicon recommended.** QMD runs on CPU but embedding and re-ranking are painfully slow without Metal/GPU acceleration.

## Configuration

### 1. Install QMD

```bash
npm install -g @tobilu/qmd
```

Or with Bun:

```bash
bun install -g @tobilu/qmd
```

### 2. Index your vault

```bash
# Add your vault as a searchable collection
qmd collection add /path/to/your/vault

# Generate vector embeddings (downloads ~2 GB of models on first run)
qmd embed
```

The first `qmd embed` takes a few minutes on Apple Silicon for ~500 files. Subsequent runs only process new or changed files.

### 3. Connect to your assistant

QMD runs as an MCP server. Add it to your assistant's configuration:

**Claude Code** (`~/.claude/settings.json`):

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

**Claude Desktop** (`claude_desktop_config.json`):

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

**Claude Code Plugin:**

```bash
claude plugin marketplace add tobi/qmd
claude plugin install qmd@qmd
```

**HTTP Transport** (persistent background service):

```bash
qmd mcp --http --daemon       # Start on localhost:8181
qmd mcp stop                  # Stop
```

### 4. Add to your instruction file

Add QMD to the `## Connected Tools` section of your instruction file:

```markdown
- **QMD (vault search)** -- Local semantic search over the vault. MCP
  tools: `query` (combined search), `get` (retrieve file by path or
  docid), `multi_get` (glob patterns), `status` (index health). Three
  query types: `lex` (BM25 keyword, supports `"phrases"` and
  `-negation`), `vec` (natural language semantic), `hyde` (hypothetical
  answer passage for nuanced topics). Use for discovery — when you
  don't know which file you need. Use direct reads for known paths.
```

### 5. Add search strategy rules (optional)

If you want your assistant to follow specific search patterns, create `Knowledge/Jesse-Guidelines/Vault-Search-Guidelines.md`:

```markdown
# Vault Search (QMD)

Use QMD as the first step when looking up anything in the vault — people,
projects, past decisions, reminders, or any reference material. Do not
read entire directories or guess at file paths.

## When to use QMD

- **Finding a file:** `query` with `lex` + `vec` sub-queries, then `get`
  the top result.
- **Checking for duplicates:** `lex` search for the key term before
  creating new files.
- **Broad discovery:** `query` with all three types (`lex` + `vec` +
  `hyde`) and higher result limits.

## When NOT to use QMD

- **Known file paths** — Read directly. Dashboard.md, Today.md, and
  files you can name from memory don't need a search step.
- **Simple directory listing** — Use `ls` or glob patterns.

## Search strategy

1. **Known keyword:** `lex` query for fast BM25 matching.
2. **Conceptual question:** `vec` query for semantic similarity.
3. **Complex/nuanced topic:** Combine `lex` + `vec` + `hyde` for
   best recall.

## Important notes

- QMD returns snippets, not full files. Always retrieve the full document
  with `get` after finding it.
- The index is not live. Run `qmd update` after significant vault changes.
- First sub-query in a search gets 2× weight — put your strongest
  signal first.
```

Then add a one-line pointer in your instruction file's `## Rules` section:

```markdown
### Vault Search (QMD)

Follow [[Knowledge/Jesse-Guidelines/Vault-Search-Guidelines]] when searching the vault.
```

## Vault Structure

This recipe adds:

```
Knowledge/
└── Jesse-Guidelines/
    └── Vault-Search-Guidelines.md    # (optional) Search strategy rules
```

No other vault files are needed. QMD's index and models live outside the vault in `~/.cache/qmd/`.

## How It Works

QMD indexes every `.md` file in your vault into a local SQLite database. It provides three search approaches through a single `query` tool:

- **`lex`** — BM25 keyword search. Fast, exact. Supports `"quoted phrases"` and `-negation`. Good for names, specific terms, file lookups.
- **`vec`** — Semantic vector search. Finds conceptually related content even when exact words don't match. A search for "what did I decide about the house coverage situation" finds notes about insurance, the broker conversation, and the renewal deadline — even if none of them contain the search words.
- **`hyde`** — Hypothetical document embedding. You write a 50–100 word passage that looks like the answer you're seeking. Often the most powerful for nuanced topics.

Combine them in a single query for best results. The first sub-query gets 2× weight:

```json
[
  { "type": "lex", "query": "\"connection pool\" timeout -redis" },
  { "type": "vec", "query": "why do database connections time out under load" },
  { "type": "hyde", "query": "Connection pool exhaustion occurs when all connections are in use..." }
]
```

Additional tools:

- **`get`** — Retrieve a full document by file path or doc ID from search results.
- **`multi_get`** — Retrieve multiple documents by glob pattern.
- **`status`** — Check index health: document count, embedding coverage, pending items.

### Keeping the index fresh

QMD doesn't watch for file changes. After your assistant writes to the vault (or you edit files manually), update the index:

```bash
qmd update    # Re-scan for new/changed files
qmd embed     # Generate embeddings for anything new
```

Both commands are incremental and fast.

### Optional: collection context

Context descriptions help QMD's semantic models understand your vault structure:

```bash
qmd context add qmd://your-vault/ "Personal knowledge vault with projects, contacts, meeting notes, and daily tasks"
qmd context add qmd://your-vault/projects "Active project files with status tracking and TODOs"
qmd context add qmd://your-vault/knowledge/people "Contact directory organized by relationship type"
```

## Tips and Gotchas

**Keyword search works without embeddings.** If you just want fast BM25 search, `lex` queries work immediately after `qmd collection add` — no embedding step needed. You can skip the model downloads entirely if semantic search isn't worth the disk space.

**Embedding on first run takes time.** The initial `qmd embed` downloads ~2 GB of models, then processes every file. A few minutes on Apple Silicon for ~500 files. Much longer on CPU-only machines. Subsequent runs are incremental.

**Score interpretation.** QMD normalizes scores to 0.0–1.0. Above 0.8 is highly relevant. Between 0.5–0.8 is moderate. Below 0.5 is likely noise. Use `minScore` in queries to filter.

**Token savings are significant.** For large vaults, QMD's snippet-based results reduce context usage by 90%+ compared to reading full files. Instead of 15,000 tokens of full documents, you get the ~500 tokens that matter.

**QMD is vault-agnostic.** It indexes any directory of Markdown files. It doesn't know about Obsidian, wiki-links, or frontmatter — it treats each `.md` file as a document and indexes its text.

**Models live in `~/.cache/qmd/models/`.** Delete them to free disk space; QMD re-downloads on next use. The SQLite index is in `~/.cache/qmd/` by default.

**Multiple vaults.** You can index multiple directories as separate collections:

```bash
qmd collection add /path/to/personal-vault    # creates "personal-vault"
qmd collection add /path/to/work-notes        # creates "work-notes"
qmd search "query" -c personal-vault           # search one collection
qmd search "query"                             # search everything
```

**Testing from the command line.** Verify your setup before connecting to an assistant:

```bash
qmd search "your search term" -n 5       # Keyword search
qmd vsearch "conceptual question" -n 5   # Semantic search
qmd query "complex question" -n 5        # Hybrid with re-ranking
qmd status                                # Index health
```
