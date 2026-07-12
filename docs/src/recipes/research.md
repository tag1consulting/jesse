# Research

Structured, source-backed research output saved to `Projects/Research/` as standalone files.

## The Problem

AI research output tends toward unsourced text with inconsistent depth. Product comparisons collapse variants, conflicts get silently resolved, and gaps go unmentioned.

## The Solution

A guidelines file in your vault that Jesse reads whenever research is requested, producing scoped, cited reports with actionability sections.

## Vault Structure

```
Knowledge/
  Jesse-Guidelines/
    Research-Guidelines.md   # Research output standards and citation rules
Projects/
  Research/                  # Completed research output (standalone files)
```

## Configuration

The template already includes research. JESSE.md has a pointer in the Rules section:

````markdown
### Research

Follow [[Knowledge/Jesse-Guidelines/Research-Guidelines]] for all research tasks. Any inbox item or instruction
that asks to research, investigate, explain, or answer a substantive question produces a file
in `Projects/Research/`, not a conversational reply.
````

## The Guidelines File

The template ships a full research methodology at [`template/Knowledge/Jesse-Guidelines/Research-Guidelines.md`](https://github.com/tag1consulting/jesse/blob/main/template/Knowledge/Jesse-Guidelines/Research-Guidelines.md) -- too long to embed here, and canonical in the template rather than in this page. What it covers:

- **Vault first** -- search the vault (QMD or grep) before any web search; previously extracted knowledge with citations counts as a source, with a freshness heuristic for when to re-search.
- **Scope confirmation** -- confirm scope on ambiguous requests; in unattended runs (scheduled tasks, inbox processing), make the most reasonable interpretation, record it in the scope statement, and never stall.
- **Writing quality** -- narrative flow, no filler, specific over general: numbers, dates, names.
- **Critical thinking** -- question the premise, show reasoning, identify gaps, name fallacies.
- **Source hierarchy** -- four tiers (primary > institutional > quality journalism > everything else with caveats), inline trustworthiness ratings, numbered citations.
- **Web search mandate** -- training knowledge is never a citable source; every non-trivial fact traces to a retrieved, dated, linkable source (or a cited vault extraction).
- **Multilingual sourcing** -- mandatory for non-English contexts; "sparse in English" means the search stopped too early.
- **Structural hints by research type** -- technical, market/competitive, and domain research patterns.
- **Devil's advocate, three layers** -- steelman, best counterargument, nuanced resolution.
- **Pre-delivery quality check** -- a self-review checklist (source monoculture, blurred epistemic lines, stale sources, missing "so what", orphaned tangents).
- **Report skeleton** -- scope statement, executive overview (scaled to length), body, devil's advocate, summary, actionability (never skipped), one or more appendices for tangents, source list.
- **Archive footer** -- every research file ends with the checkbox footer from [[Knowledge/Jesse-Guidelines/Archive-Footer-Guidelines]] so it can be processed from Obsidian.

Copy the file from the template and edit it for your needs; the version in your vault is the one Jesse follows.

## How It Works

1. Drop a research request in Inbox/ or ask Jesse directly.
2. Jesse reads `Knowledge/Jesse-Guidelines/Research-Guidelines.md`, scopes the research, gathers sources, and writes a structured report.
3. Output lands in `Projects/Research/` with a summary and actionability section.
4. Any inbox item or instruction that asks to research, investigate, explain, or answer a substantive question produces a file in `Projects/Research/`. The conversational summary is secondary to the written report.

## Tips and Gotchas

**Don't collapse variants.** If something comes in multiple configurations, show all of them.

**Non-English sources matter.** Especially for regulatory, standards, or international topics.

**Gaps are findings.** "No data exists" is valuable -- say so explicitly.

**The actionability section is never skipped.** Even when the answer is "nothing to do yet."

**The guidelines file is yours to edit.** Different citation style, depth, domain needs -- change it directly.
