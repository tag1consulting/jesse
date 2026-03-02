# The Instruction File

The instruction file is where the magic happens. It's a markdown file at the root of your vault that tells the AI agent who you are, what your priorities look like, and exactly what to do when you start a session.

The full starter version is in the template at [`template/JESSE.md`](https://github.com/tag1consulting/jesse/blob/main/template/JESSE.md). This page explains the key sections and the thinking behind them.

## Core Principles

Everything starts with four rules:

1. **Act on inbox instructions immediately.** When a note says "research X" or "draft Y," do the work now. Don't just file it.
2. **Track everything until acknowledged.** New items stay visible until you sign off.
3. **Show your judgment calls.** The agent must tell you what it decided and what it chose not to do.
4. **Always write in Markdown.** Everything stays editable and portable.

The first principle is the one that took the most iteration. Early versions said "process inbox items" and the agent would dutifully file them into project folders without doing anything. Now if you drop a note that says "research options for renewing the office insurance," you start your day with a useful writeup, not a TODO item.

The third principle is a safety mechanism. LLMs will make prioritization decisions you disagree with. The only fix is requiring the agent to surface its reasoning so you can course-correct.

## The Start-of-Day Routine

The routine has three phases: gather, process, produce.

**Gather:** Read the inbox, scan email, check messaging apps, pull today's calendar, review the dashboard and reminders. This is reading only. No decisions yet.

**Process:** Act on inbox instructions. Extract TODOs from email and messages. Flag items relevant to today's meetings. Map available work time around meetings.

**Produce:** Rebuild Today.md, sync Dashboard.md, archive processed inbox files, and deliver a morning briefing that surfaces new items, decisions, and anything needing your input.

## Customizing the Instruction File

The template is a starting point. Strip out what doesn't apply to you and add sections for your situation. Common additions:

- **Connected tools:** Instructions specific to your email provider, messaging apps, calendar, etc. Each integration gets a section describing how to use it and what to look for.
- **Weekly routines:** Recurring tasks that happen on specific days (vault maintenance, supply checks, health audits).
- **Communication rules:** Whether the agent can send messages or only draft them. (We recommend draft-only.)
- **Preferences:** Your communication style, formatting rules, things the agent should never do.

## Tips

**Start simple.** Your first instruction file should be short. Add rules as you hit situations that need them. You won't know what rules you need until you encounter the situations.

**Be specific.** "Scan email" is vague. "Check jeremy@company.com for action items; if it's still in the inbox, it's not done" is specific. The more precise your instructions, the better the output.

**Iterate constantly.** Every time the agent does something you'd do differently, add or refine a rule. The instruction file is a living document. Revise it dozens of times. Ask the agent to help you improve it.

**Use the agent to test changes.** After editing the instruction file, ask the agent to read it back and tell you if anything is ambiguous or contradictory. It's surprisingly good at finding holes in its own instructions.
