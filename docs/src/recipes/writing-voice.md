# Writing Voice

Catch and fix AI writing tells before publishing or sending anything.

## The Problem

AI-generated prose has a recognizable voice: em dashes everywhere, the same vocabulary fingerprints, grandiose framing, symmetrical structure. Readers increasingly recognize it, and it undermines credibility.

## The Solution

A checklist file in your vault that Jesse reads before finalizing any prose meant for an external audience.

## Vault Structure

```
Knowledge/
  Jesse-Guidelines/
    Writing-Voice-Guidelines.md    # Writing voice and anti-AI-tells checklist
```

## Configuration

The template already includes writing voice. JESSE.md has a one-line pointer in the Rules section:

````markdown
### Writing Voice

Before finalizing any prose that will be published, shared, or sent externally, check the output against [[Knowledge/Jesse-Guidelines/Writing-Voice-Guidelines]].
````

## The Guidelines File

The template includes this file at `Knowledge/Jesse-Guidelines/Writing-Voice-Guidelines.md`:

````markdown
# Writing Voice Guidelines

All writing produced in this vault that will be published, shared, or sent externally must read like a human wrote it. Before finalizing any prose, check the output against these guidelines. If it sounds like a press release, a corporate memo, or a Wikipedia article about itself, rewrite it. Read it out loud -- that's the fastest test.

Em dashes are the biggest flag -- LLMs use them constantly. Use commas, semicolons, parentheses, or restructure the sentence instead. Avoid staccato parallel structure where every sentence follows the same pattern. Watch for compulsive rule-of-threes grouping, colon-terminated setup sentences ("Here's the thing:"), and sentences that all run the same length.

Specific words are LLM fingerprints and should never appear in output: delve, tapestry, pivotal, underscore, landscape, foster, crucial, enhance, multifaceted, comprehensive, intricate, nuanced, holistic, synergy, leverage, paradigm, robust, seamless, elevate, groundbreaking, testament, commendable, noteworthy, innovative, realm, meticulous, arguably, notably. Also: "utilize" (say "use"), "facilitate" (say "help" or "enable"), "leverage" (say "use").

LLMs puff up importance reflexively: "stands as a testament to," "plays a vital role in," "represents a significant step forward." If the sentence works without the intensifier, cut it. Also cut editorial previews: "It's important to note that," "It is worth mentioning," "The key takeaway here is." Just say the thing.

Watch for negative parallelism ("It's not X. It's Y."), compulsive summaries ("In summary," "In conclusion," "Overall,"), overused conjunctions at sentence starts ("Moreover," "Furthermore," "Additionally"), false ranges ("From X to Y"), and symmetrical comparisons where every pro gets a con. Real writing is lopsided. Also avoid formatting tells: emoji in headings, excessive bold, numbered lists where prose would be more natural, and every paragraph the same length.

This file is yours to edit. Add words your industry overuses, remove items that don't apply to your writing context, add style preferences specific to your voice. The instruction file just points here -- changing this file changes the behavior immediately.
````

## How It Works

1. You ask Jesse to draft, write, or edit prose for external use.
2. Jesse writes the content, then checks it against `Knowledge/Jesse-Guidelines/Writing-Voice-Guidelines.md` before delivering.
3. Flagged patterns get rewritten. The final output reads like a person wrote it.

## Tips and Gotchas

**Em dashes are the biggest tell.** Train yourself to spot them too -- they'll creep back in.

**The vocabulary list is a starting point.** Add industry jargon your tools overuse and remove words you actually use naturally.

**Read it out loud.** If it sounds like a press release, it fails the test.

**This applies to drafts too.** Even if you'll edit before sending, starting from clean prose saves time.

**The guidelines file is yours to edit.** Different voice, different audience, different tells -- change it directly.
