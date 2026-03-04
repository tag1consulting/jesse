# Research

Produce structured, source-backed research output that's accurate and accessible -- written so you can read it and come away understanding the topic, not just holding a pile of citations.

## The Problem

When you ask an AI to "research X", you get a wall of text with vague claims, no sources, inconsistent depth, and no clear takeaway. Product comparisons collapse variant lines into single numbers. Conflicting data gets silently resolved. Gaps go unmentioned. The output looks thorough but doesn't hold up when you need to make a decision from it.

## The Solution

A structured set of research guidelines that Jesse follows whenever research is requested. Output goes to `Projects/Research/` as a standalone file, with consistent structure, source discipline, and an actionability section tied to whatever triggered the research.

## JESSE.md Configuration

Add the following to the **Rules** section of your instruction file:

````markdown
### Research

When research is requested (from Inbox/, conversation, or any other trigger), follow these guidelines for all research output.

#### Structure

Start with a scope statement: what's covered, what's excluded, and why. Follow with a 2-3 sentence executive overview that delivers the takeaway up front. The body should explore the topic from multiple angles, choosing angles relevant to the reason the research was requested when known. Match depth to content per section -- use accessible overviews where a concept just needs to land, and include technical deep-dives where the subject demands full understanding. Not every section needs both.

#### Variants and Comparisons

When a product or system comes in multiple tiers, configurations, or variants, identify all of them early. Don't collapse a product line into a single spec number -- a "Max" chip with 32-core and 40-core GPU options has two different bandwidth figures, and using just one misrepresents the picture. Build comparison structures (tables, side-by-side breakdowns) that show the full variant landscape so the reader can locate themselves in it.

#### Sources

Find multiple sources and prefer primary over secondary: manufacturer specs, published papers, official announcements, and direct interviews over blog recaps and aggregator summaries. Non-English sources are valid and sometimes critical -- even when a topic appears US- or English-centric, actively check whether relevant non-English perspectives exist. Don't force irrelevant sources, but don't default to English-only either.

Note each source's publication date and flag when key claims rest on outdated data. Rate source trustworthiness inline and state why: an official spec sheet outranks an anonymous forum post, a peer-reviewed study outranks a vendor whitepaper. Use numbered citations [1] [2] inline throughout the text, with a full source list (URL, title, date, language if non-English) at the end.

#### Accuracy and Honesty

Distinguish reported fact from analyst speculation from your own synthesis, and make the boundaries obvious. When sources conflict, surface both claims with context on methodology -- don't silently pick a winner or split the difference. When a conflict is noticed mid-analysis, resolve it inline: state both figures, identify which source is more authoritative and why, use the stronger source for any calculations or conclusions, and flag the remaining uncertainty so the reader knows the number isn't settled.

Explicitly note gaps: if no source covers an obvious question, or if all available data is outdated or single-sourced, say so. "No independent benchmarks exist yet" is a finding, not an omission. But if a gap could be closed with additional source work -- a GitHub repo, a changelog, a release blog -- close it rather than flagging it as unresolved.

#### Output

End with a detailed summary that draws all threads together into a coherent picture, followed by an actionability section. If the research was triggered by a decision, state what the findings mean for that decision. If there's nothing actionable yet, say that -- the section is never skipped.

Always save research output to `Projects/Research/` as a standalone file.
````

## Vault Structure

```
Projects/
  Research/              # Completed research output (standalone files)
```

Create the `Projects/Research/` directory if it doesn't exist. Each research task produces one file here, named descriptively: `Apple-Silicon-Comparison.md`, `Home-Insurance-Options.md`, etc.

## How It Works

1. You drop a note in Inbox/ saying "research X" or ask Jesse directly.
2. Jesse scopes the research, gathers sources, and writes a structured report following the guidelines above.
3. The output lands in `Projects/Research/` as a standalone markdown file.
4. The summary and actionability section tell you what to do next -- or explicitly say there's nothing to do yet.

## Tips and Gotchas

**Research is for human consumption.** The goal is understanding, not completeness for its own sake. If a section doesn't help you make a decision or understand the topic, it shouldn't be there.

**Don't collapse variants.** This is the most common failure mode. If something comes in multiple configurations, the research should show all of them, not pick one representative number.

**Non-English sources matter.** Especially for regulatory, technical standards, or international topics. Jesse will actively look for them when relevant.

**Gaps are findings.** "No data exists on this" is valuable information. The guidelines require Jesse to say so explicitly rather than papering over missing information.

**The actionability section is never skipped.** Even when the answer is "nothing to do yet", that's worth stating. It prevents the research from sitting in a folder with no clear next step.
