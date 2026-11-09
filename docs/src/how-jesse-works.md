# How Jesse Works

Jesse has three parts. Understanding how they fit together makes everything else on this site obvious.

## The Mental Model

**The vault is the memory.** A folder of markdown files holds everything Jesse knows: your tasks, your projects, the people you work with, your notes, your reminders. AI sessions are stateless -- the agent remembers nothing from yesterday. The vault is what persists, and unlike an AI's memory, it's deterministic: files change only when you change them or when the agent does during a session you're directing. You can open any file, read it, and edit it in any editor, with or without AI involved.

**The instruction file is the process.** One markdown file at the vault root (`JESSE.md`, or `CLAUDE.md` for Claude's Cowork mode) tells the agent who you are, what your rules are, and what to do when a session starts. It stays deliberately lean -- core principles, rules, preferences -- and points to detailed procedures in `Knowledge/` files that the agent loads only when a task calls for them.

**The agent is the engine.** Any AI agent that can read files, write files, and call tools can run Jesse. It brings no knowledge of you; everything personal comes from the vault. That's what makes the system portable -- switch agents or providers, and your memory and process come with you.

## A Morning, Traced

You open a session and say "good morning." Here's what happens:

1. The agent reads the instruction file. Now it knows your rules, your priorities, and where everything lives.
2. It loads the daily routine from `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md`.
3. It scans your connected tools -- email, calendar, messaging -- for anything new since last session. Verbose scans are delegated to subagents that return compact summaries, so your session's context stays focused.
4. It checks `Inbox/` for notes you dropped in from your phone or desktop. A note saying "research X" triggers the research. A note saying "remind me Thursday" creates the reminder.
5. It reconciles your task list: items you checked off get closed out, new action items from email and Slack get proposed (not silently added -- you confirm what goes on your plate).
6. It rebuilds `Today.md` with your schedule and tasks, prepares agendas for today's meetings, and updates the project and people files that today's information touched.
7. It delivers the briefing:

```
📅 Calendar -- Today
- 10:00 AM -- 1:1 with Sally (prep: review her project update from Friday)
- 2:00 PM  -- Team standup

📧 Email -- 6 new
🔴 Needs response today: Client asking about timeline for deliverable
🟡 This week: Vendor sent contract for review
🟢 FYI: 4 automated notifications

💬 Slack
- @mention in #projects -- someone asking about deploy schedule
- DM from manager with a question about budget

🔔 Reminders
- Follow up with Alex on proposal (set 3 days ago)
```

Every line traces back to a file or a scan: the calendar block from your calendar tool, the meeting prep note from the people file the agent keeps on Sally, the email triage from rules in your instruction file about what's urgent, the reminder from a date-prefixed file in `Knowledge/Reminders/`.

You scan the briefing, correct what's wrong ("that vendor email is actually urgent"), and the agent updates the files. The whole loop takes a few minutes.

## Why It Gets Better Over Time

The instruction file is a living document. When the agent misclassifies an email, you correct it and add a rule: "Emails from this client are always at least yellow." When it drafts in the wrong tone, you refine the writing-voice guidelines. Every correction makes the system more specifically yours.

This is the core bet of the whole design: **the quality of the output is a function of the quality of your instructions.** A vague instruction file produces vague results. A specific one -- built up correction by correction over a few weeks -- produces something that feels like delegating to a competent assistant who knows how you work.

[Recipes](./recipes/index.md) accelerate this. Each one is a pattern extracted from real daily use -- email triage, meeting agendas, research standards, a people directory -- that you can adopt piecemeal instead of discovering from scratch.

## The Safety Design

Jesse is deliberately not a "god mode" agent:

- **Drafts, never sends.** Replies and messages are written to `Projects/drafts/` and wait for your review. Nothing goes out without your explicit approval.
- **Sandboxed with permission prompts.** The agent runs in a sandboxed environment and asks before sensitive actions. It can't send a message, make a purchase, or delete a file on its own.
- **Judgment calls are surfaced.** A core principle in the instruction file requires the agent to tell you what it decided and what it chose not to do, so you can catch a wrong call before it propagates.
- **Everything is inspectable.** Because state lives in files, you can always see exactly what the system knows, diff what changed, and revert with git if you keep the vault in version control.

The reasoning is spelled out in [Design Philosophy](./design-philosophy.md).

## What the Files Look Like

```
Dashboard.md              Priority-sorted task list (Urgent / This Week / Waiting / Backlog)
Today.md                  Today's schedule and tasks, rebuilt each morning
JESSE.md                  The instruction file
Inbox/                    Quick capture -- drop a note, the next session acts on it
Projects/                 One file per project, plus research output and drafts
Knowledge/                People directory, reminders, and the agent's guidelines
```

The full layout, naming conventions, and archive pattern are in [Vault Structure](./vault-structure.md). The instruction file's anatomy is in [The Instruction File](./instruction-file.md).

## Ready to Try It

The [Quick Start](./getting-started.md) takes about 10 minutes: copy the template, edit the instruction file, point your agent at the folder, and run your first session.
