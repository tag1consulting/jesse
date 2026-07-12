# Research Guidelines

## Vault First

Before any web search, query QMD (or another configured search tool -- never globs) to find prior research, knowledge collection entries, people files, project files, and reminders. The vault is accumulated knowledge -- research that ignores it produces generic results instead of results calibrated to the user's situation. Reference vault context in reports alongside external findings. Be context-aware about loading: retrieve the 2-5 most relevant files, not everything that matches.

## Scope Confirmation

Before beginning research, confirm scope with the requester when the instruction is ambiguous or broad. State what you plan to cover, what you plan to exclude, and your intended depth. For focused, specific requests ("what's the max throughput of X?") this is unnecessary -- just do the work. For open-ended or multi-faceted topics ("research our options for Y"), a one-paragraph scope confirmation prevents misalignment before effort is spent. Keep it brief: this is a sanity check, not a planning document.

**Unattended mode.** When research is triggered with no one available to answer -- a scheduled task, overnight inbox processing, batch work -- never block on scope confirmation and never defer the work to a backlog. Make the most reasonable interpretation, treating the instruction as the minimum scope rather than the maximum, and record the interpretation in the report's scope statement: what was assumed, what was deliberately excluded, and any open questions the requester should answer if the scope missed. If the instruction is so ambiguous that no reasonable interpretation exists, still produce a file in `Projects/Research/` -- a short scoping note stating the interpretations considered and what's needed to proceed. Unattended research must always leave a file trail, never a silent stall.

## Writing Quality

Research must read like something a sharp analyst wrote. Narrative flow over section checklists -- each section should build on the last, not repeat a template. Varied sentence structure. No filler words, no hedge-stacking ("it could potentially perhaps be argued that..."). Be specific: include numbers, dates, names, version numbers, scores, percentages, named organizations, and named individuals. Every factual claim should carry its specifics. Use tables for comparison and prose for explanation -- don't use one where the other belongs.

## Critical Thinking

Every report must model and teach critical thinking. This is the most important section of these guidelines.

**Question the premise.** Before researching the answer, examine the question. What assumptions does it contain? Are they valid? If the question is "which is better, X or Y?" -- is that the right framing, or is there a Z nobody mentioned? If the framing is narrow, expand beyond it.

**Show the reasoning, not just the conclusion.** The reader should be able to evaluate the claim themselves after reading the report. Don't just say "Source A is more credible" -- explain what makes it more credible (methodology, sample size, independence, track record). Don't just say "this claim is disputed" -- show what the dispute is about and what each side gets right.

**Identify what's missing.** Every argument has gaps. Name them. What evidence would strengthen or weaken the claim? What populations, contexts, or timeframes aren't represented? What questions does the research raise that it doesn't answer?

**Name logical fallacies naturally.** Don't append a "fallacies detected" checklist. When an argument conflates correlation with causation, or when a source appeals to authority instead of evidence, call it out inline as part of the analysis. The reader should come away recognizing these patterns, not just knowing the conclusion.

**Give concrete examples.** Abstract critical thinking advice is useless. Show how to think critically about *this specific topic* -- what questions to ask about *this* data, what to watch for in *this* domain, what the strongest counterargument is to *this* claim. Leave the reader better equipped to evaluate future claims in this area.

## Source Hierarchy and Multilingual Research

Research standard is exhaustive literature review, not survey. Multiple sources per major claim. Four-tier source priority:

1. **Primary sources** -- manufacturer specs, published papers, official announcements, direct measurements, original datasets, court filings, regulatory documents.
2. **Institutional sources** -- government agencies, standards bodies, academic institutions, established research organizations.
3. **High-quality journalism** -- named reporters at established outlets with domain expertise, investigative pieces, long-form analysis.
4. **Everything else** -- with explicit caveats about reliability. Blog posts, forum discussions, social media, and vendor marketing can provide leads but are never authoritative alone.

Web search is mandatory for all non-trivial factual claims that the vault cannot already source. Training knowledge provides context and helps evaluate sources, but it is not itself a citable source -- every non-trivial fact in the final document must trace to a retrieved, dated, linkable source. Knowledge previously extracted into `Knowledge/` counts, provided it carries its own citations: research builds on research, and repeating a search the vault already answered wastes effort. When reusing extracted knowledge, cite both the vault file and the original source with its date, so staleness stays visible.

Apply a freshness heuristic when deciding whether to re-search: the older the extracted data and the faster-moving the domain, the more a new search is warranted. Stable facts (historical events, physical constants, settled standards) rarely need re-verification. Volatile facts (prices, versions, benchmarks, regulations, market data) should be re-searched when the original source's age exceeds the domain's rate of change -- the 2-year staleness bar in the pre-delivery check is a reasonable default. Vault knowledge with no source attached is a lead, not a citation: verify it externally before it carries a conclusion.

If web search is unavailable, state that limitation prominently at the top of the document rather than filling gaps from memory.

Rate source trustworthiness inline and state why: an official spec sheet outranks an anonymous forum post, a peer-reviewed study outranks a vendor whitepaper. Note each source's publication date and flag when key claims rest on outdated data. Use numbered citations [1] [2] inline throughout the text, with a full source list at the end.

**Multilingual sourcing is mandatory** for any topic touching a non-English-speaking country or context -- especially the user's own country of residence when it isn't English-speaking. Search in the relevant local language first, then neighboring languages with strong traditions in the domain, then English. "Sparse in English-language sources" is never an acceptable finding -- it means you stopped too early. Even for topics that appear US- or English-centric, actively check whether relevant non-English perspectives exist (European standards bodies, non-English technical communities, local regulatory filings). Don't force irrelevant sources, but don't default to English-only either.

## Structural Hints by Research Type

The structure of a research document should adapt to what's being researched. These are starting points, not rigid templates -- use judgment about which sections actually serve the topic.

**Technical research** (evaluating tools, architectures, implementation approaches): Prioritize current ecosystem landscape, architectural patterns and trade-offs, integration considerations, performance characteristics, and implementation maturity. A comparison matrix is usually more useful than prose for head-to-head evaluations.

**Market or competitive research** (understanding a market, evaluating competitors, assessing positioning): Prioritize customer needs and behavior patterns, competitive landscape and positioning, market dynamics and trends, and strategic implications. Focus on *so what* -- what the findings mean for a decision.

**Domain research** (understanding an industry, regulatory environment, or specialized field): Prioritize the regulatory and compliance landscape, key ecosystem players and their relationships, industry-specific terminology and concepts, and emerging trends. Write for a reader who's intelligent but not domain-expert.

Many research requests blend types. A question like "should we adopt tool X?" is both technical (does it work?) and market (who else uses it? what are the alternatives?). Let the question drive the structure, not a category label.

## Variants and Comparisons

When a product or system comes in multiple tiers, configurations, or variants, identify all of them early. Don't collapse a product line into a single spec number -- a "Max" chip with 32-core and 40-core GPU options has two different bandwidth figures, and using just one misrepresents the picture. Build comparison structures (tables, side-by-side breakdowns) that show the full variant landscape so the reader can locate themselves in it.

## Devil's Advocate: Three Layers Deep

Every report includes a structured devil's advocate analysis in three layers:

- **Layer 1 (claim):** State the dominant position at its strongest -- steelman it. Present the best version of the argument, not a strawman. The reader should think "yes, that's compelling" before moving to the next layer.
- **Layer 2 (devil's advocate):** Best counterargument. Who disagrees, why, what evidence undermines the claim. This should make the reader genuinely uncertain. If it doesn't create real doubt, it's not strong enough.
- **Layer 3 (refutation/resolution):** Does the claim survive? Where does it hold, where does it break? The resolution should be more nuanced than either layer alone -- rarely a clean win for one side.

This can be a standalone section or woven throughout the report on a per-claim basis. The point is intellectual honesty -- research that only argues one side is advocacy, not analysis.

## Stress-Testing and Going Beyond the Ask

Do not validate assumptions -- stress-test them. If the user's framing is narrow, expand beyond it. Research that tells someone what they already think is worthless. Challenge the premise, explore adjacent surfaces, find counterintuitive findings.

An "editorial framing" section assessing the user's intuition is acceptable briefly but must never substitute for research depth. The bulk of any report is evidence and analysis, not commentary on what the user thought going in.

## Appendices for Tangents

When research uncovers an interesting tangent -- something related to the topic but not directly answering the core question -- add one or more appendices at the end of the report. A report can carry several; give each tangent its own clearly labeled appendix rather than merging unrelated material.

Good appendix material: historical context that explains why something is the way it is, adjacent fields that share surprising connections, a named researcher whose work is worth following, a cultural or regional tradition that provides unexpected perspective, a technical deep-dive that would interrupt the main report's flow but rewards the curious reader.

Appendices are self-contained (readable independently) and labeled with what they cover. They're bonus knowledge, not padding -- only include a tangent when it's genuinely interesting on its own.

## Handling Conflicts and Gaps

Distinguish reported fact from analyst speculation from your own synthesis, and make the boundaries obvious. When sources conflict, surface both claims with context on methodology -- don't silently pick a winner or split the difference. Resolve conflicts inline: state both figures, identify which source is more authoritative and why, use the stronger source for any calculations or conclusions, and flag the remaining uncertainty so the reader knows the number isn't settled.

Explicitly note gaps: if no source covers an obvious question, or if all available data is outdated or single-sourced, say so. "No independent benchmarks exist yet" is a finding, not an omission. But if a gap could be closed with additional source work -- a GitHub repo, a changelog, a release blog, a non-English source -- close it rather than flagging it as unresolved.

## Pre-Delivery Quality Check

Before finalizing, self-review the document for these common failure modes:

- **Unsourced claims:** Every non-trivial factual assertion should have a citation. If you find one without, either add a source or rewrite it as clearly labeled synthesis/analysis.
- **Source monoculture:** If all citations in a section come from the same author, outlet, or vendor, note the limitation and look for independent corroboration.
- **Blurred epistemic lines:** Re-check that the reader can always tell what's a reported fact, what's analyst opinion, and what's your synthesis. These boundaries are easy to blur during drafting.
- **Stale sources driving conclusions:** If a key conclusion rests on a source more than 2 years old, flag it -- the conclusion may still hold, but the reader should know the evidence isn't recent.
- **Missing "so what":** Every section should connect back to the reason the research was requested. If a section is informative but doesn't serve the triggering question, either cut it or explain why it's relevant context.
- **Orphaned tangents:** Interesting material that doesn't serve the triggering question belongs in an appendix (there can be more than one), not in the body and not silently cut. Before deleting a section for relevance, check whether it earns a place as an appendix.

## Report Structure

Every research report follows this skeleton:

1. **Scope statement** -- what's covered, what's excluded, and why.
2. **Executive overview** -- For short research pieces (under ~1000 words), a 2-3 sentence overview that delivers the takeaway is sufficient. For longer pieces, include: a short narrative overview of the key findings (2-3 paragraphs max), a bullet list of the most important findings (not all findings -- just the ones that matter most for the triggering question), and if applicable, a bullet list of actionable recommendations. For documents with more than 4-5 major sections, include a table of contents after the executive summary.
3. **Body sections** -- explore the topic from multiple angles, choosing angles relevant to the reason the research was requested. Structure depth per section based on what the material needs: accessible overviews where a concept just needs to land, technical deep-dives where the subject demands full understanding. Match the depth to the content.
4. **Devil's advocate** -- three layers as described above (or woven into body sections).
5. **Summary** -- draws all threads together into a coherent picture.
6. **Actionability** -- what the findings mean for decisions. If nothing is actionable yet, say so. Never skip this section.
7. **Appendices** -- optional; one or more, for tangents worth preserving.
8. **Source list** -- numbered, with URL, title, date, author when available, language if non-English.

Always save research output to `Projects/Research/` as a standalone file.

## Archive Footer

Every research file ends with an archive footer. See [[Knowledge/Jesse-Guidelines/Archive-Footer-Guidelines]] for the exact format and processing rules. The footer lets the user signal from Obsidian whether to extract knowledge before archiving, deep-extract with a lower bar, or archive without extraction.
