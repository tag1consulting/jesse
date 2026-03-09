# The Instruction File

The instruction file is a markdown file at the root of your vault that tells the AI agent who you are, what your rules are, and where to find the procedures for each task. It's designed to be lean: only the things needed every session stay inline. Everything else lives in separate `Knowledge/` files that the agent loads on demand.

The full starter version is in the template at [`template/JESSE.md`](https://github.com/tag1consulting/jesse/blob/main/template/JESSE.md). This page explains the key sections and the thinking behind them.

## Structure

JESSE.md contains:

- **Core Principles** -- The four rules the agent reads every session (act on inbox, track until acknowledged, show judgment calls, always markdown).
- **Context Management** -- Guidelines for loading files on demand, delegating verbose tools to subagents, and using direct reads for known paths.
- **Routines** -- Short summaries that point to detailed procedures in Knowledge/ files. The Start of Day routine is a three-line phase summary with a link to `[[Knowledge/Jesse-Guidelines/Start-of-Day-Routine]]`. Weekly maintenance is a one-liner pointing to `[[Knowledge/Jesse-Guidelines/Weekly-Vault-Maintenance]]`.
- **Rules** -- Naming conventions, timestamps, draft lifecycle, and pointers to Knowledge/ files for people KB, research, writing voice, and meeting agendas.
- **Preferences** -- Your communication style and working expectations.
- **Connected Tools** -- What's available in your environment (populated during first run).

`Knowledge/` serves double duty: it's both your personal knowledge base (people you know, reminders) and the home for all agent behavior rules in `Jesse-Guidelines/` (routines, setup procedures, and content guidelines like research standards, writing voice, and meeting agendas). Everything that's only needed situationally lives there and gets loaded when the task calls for it.

## Core Principles

Four rules the agent reads first, every session:

- **Act on Inbox/ immediately.** When a note says "research X" or "draft Y," do the work now. Don't just file it.
- **Track everything until acknowledged.** New items stay visible until you sign off.
- **Show your judgment calls.** The agent must tell you what it decided and what it chose not to do.
- **Always write in Markdown.** Everything stays editable and portable.

The third principle is a safety mechanism. LLMs will make prioritization decisions you disagree with. The only fix is requiring the agent to surface its reasoning so you can course-correct.

## On-Demand Loading

Most of JESSE.md's content is only needed situationally. First-run setup executes once. The full daily routine loads once per session. Weekly maintenance loads once per week. Loading all of it every session wastes context window budget that could go toward actual work.

The pattern: JESSE.md keeps a short summary and a `[[wiki-link]]` to the detailed procedure. The agent reads the linked file when the task calls for it. This mirrors how the system evolved in production use -- keep the instruction file lean, point to reference files, load them when needed.

Knowledge/ files referenced by JESSE.md:

| File | When loaded |
|---|---|
| `Knowledge/Jesse-Guidelines/First-Run-Setup.md` | Once, when Dashboard.md doesn't exist |
| `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md` | Once per session, at routine start |
| `Knowledge/Jesse-Guidelines/Weekly-Vault-Maintenance.md` | Once per week |
| `Knowledge/Jesse-Guidelines/People-KB-Guidelines.md` | When creating or updating a person entry |
| `Knowledge/Jesse-Guidelines/Research-Guidelines.md` | When performing research tasks |
| `Knowledge/Jesse-Guidelines/Writing-Voice-Guidelines.md` | When finalizing prose for external use |
| `Knowledge/Jesse-Guidelines/Meeting-Agenda-Guidelines.md` | When preparing meeting agendas |

Recipes add new files following the same pattern.

## The Start-of-Day Routine

The routine has three phases: gather, process, produce. JESSE.md has a three-line summary. The full orchestration lives in `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md`.

**Gather:** Read the inbox, scan email, check messaging apps, pull today's calendar, review the dashboard and reminders. Reading only -- no decisions yet.

**Process:** Act on inbox instructions. Extract TODOs from email and messages. Flag items relevant to today's meetings. Map available work time around meetings.

**Produce:** Rebuild Today.md, prepare meeting agendas, sync Dashboard.md, archive processed inbox files, and deliver a morning briefing that surfaces new items, decisions, and anything needing your input.

## Customizing

The template is a starting point. Strip out what doesn't apply and add sections for your situation. Common additions:

- **Connected tools:** Instructions specific to your email provider, messaging apps, calendar, etc.
- **Knowledge/ files:** New guidelines, reference material, or routines for capabilities you add (recipes provide these).
- **Communication rules:** Whether the agent can send messages or only draft them. (We recommend draft-only.)
- **Preferences:** Your communication style, formatting rules, things the agent should never do.

When adding a recipe, the typical pattern is: create a Knowledge/ file with the detailed guidelines, then add a one-line wiki-link pointer in the Rules section of JESSE.md. The detailed content stays out of the instruction file.

## Tips

**Start simple.** Your first instruction file should be short. Add rules as you hit situations that need them. You won't know what rules you need until you encounter the situations.

**Be specific.** "Scan email" is vague. "Check jeremy@company.com for action items; if it's still in the inbox, it's not done" is specific. The more precise your instructions, the better the output.

**Iterate constantly.** Every time the agent does something you'd do differently, add or refine a rule. The instruction file is a living document. Revise it dozens of times. Ask the agent to help you improve it.

**Edit the Knowledge/ files too.** The instruction file isn't the only thing you customize. The daily routine, maintenance tasks, people entry format, research standards, and writing voice guidelines are all in separate files you own. Change them directly -- the instruction file just points to them.

**Use the agent to test changes.** After editing the instruction file, ask the agent to read it back and tell you if anything is ambiguous or contradictory. It's surprisingly good at finding holes in its own instructions.
