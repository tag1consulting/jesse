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
- The index is refreshed by an external scheduled job, not by the agent.
  Check `qmd status` at start of session and warn if the index is
  more than 24 hours stale. Files created or edited during a session
  won't appear in search results until the next index run.
- First sub-query in a search gets 2× weight — put your strongest
  signal first.

The vault can have many hundreds of files with accumulated context. A 2-second QMD query prevents embarrassing misses like failing to find a person who already has a detailed People file, or re-researching a topic that was already covered.

This applies equally to research tasks — before launching web research on any subject, search QMD first. The vault may already have prior research, knowledge collection entries, local knowledge files, or project context that should inform (and be referenced alongside) external findings.
