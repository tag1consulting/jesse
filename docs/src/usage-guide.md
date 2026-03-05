# Using Jesse Day to Day

The Quick Start guide gets you set up. This page shows you what actually using Jesse looks like once it's running: the daily rhythm, common interactions, and how the system grows with you over time.

The examples here come from real usage. Your version will look different depending on your tools, your job, and what you care about tracking. That's the point. Jesse is a framework you shape to fit how you already work.

## The Morning Routine

This is the core of the system. You open a session, say something like "good morning" or "what do I have today," and the agent runs the full start-of-day routine.

What happens behind the scenes:

1. The agent reads your instruction file to load your rules and preferences.
2. It scans your connected tools (email, calendar, messaging) for anything new.
3. It checks your Inbox/ folder for notes you dropped in since last session.
4. It reviews your active tasks and reminders.
5. It builds (or rebuilds) Today.md and updates Dashboard.md.
6. It delivers a morning briefing summarizing what needs your attention.

The briefing groups things by urgency. A typical output looks something like:

```
📅 Calendar — Today
- 10:00 AM — 1:1 with Sarah (prep: review her project update from Friday)
- 2:00 PM — Team standup

📧 Email — 6 new
🔴 Needs response today: Client asking about timeline for deliverable
🟡 This week: Vendor sent contract for review
🟢 FYI: 4 automated notifications

💬 Slack
- @mention in #projects — someone asking about deploy schedule
- DM from manager with a question about budget

🔔 Reminders
- Follow up with Alex on proposal (set 3 days ago)
```

You scan this, correct anything the agent got wrong ("that email is actually urgent, move it to red"), and decide what to tackle first. The agent updates the files based on your feedback.

The whole process takes a few minutes. Most of the value is that you don't have to open five apps and mentally piece together your day. It's already done.

### Triggering the Routine

You don't need a specific phrase. Casual greetings work fine:

- "Morning"
- "What's on my plate today?"
- "Catch me up"
- "Run the morning routine"

Your instruction file defines what phrases trigger the routine, so you can customize this to whatever feels natural.

## Working Throughout the Day

Between routines, Jesse is useful for one-off tasks. Some examples:

**Drafting messages.** You can say "draft a reply to that email from Alex about the project timeline" and the agent writes a draft, saves it to Drafts/, and waits for you to review it before anything gets sent. Nothing leaves your outbox without your explicit approval.

**Adding tasks.** "Add a P2 task to follow up with the design team about the mockups" creates the entry in your task list with proper formatting, timestamps, and source tracking.

**Quick research.** "Summarize the Slack thread in #engineering about the outage" pulls the full conversation and gives you a condensed version.

**Meeting prep.** "What context do I need for my 2pm with Sarah?" pulls up her people file, any open tasks related to her projects, and recent communication history.

**Capturing notes.** Drop a note in Inbox/ from your phone (via Obsidian mobile, a quick-capture shortcut, or however you sync files). The next time you run a routine, the agent picks it up and acts on it. If your note says "research options for X," it does the research. If it says "remind me to call Y on Thursday," it creates a reminder.

## Adding and Managing Tasks

Tasks live in Tasks/Active.md with a simple format:

```
- [ ] P2 | Work | Follow up with design team on mockups | Due: 2026-03-10 | Source: meeting notes | (Added 2026-03-05)
```

Each task has a priority (P1 = today, P2 = this week, P3 = when possible), a context (Work or Home), a description, and metadata about where it came from and when it was created.

You can add tasks in several ways:

- **Tell the agent directly:** "Add a task to review the contract by Friday."
- **Drop a note in Inbox/:** The morning routine picks it up.
- **The agent proposes them:** When scanning email or Slack, the agent flags potential action items and asks if you want to add them to the task list.

That last point matters. The agent doesn't silently add things to your list from email. It surfaces what it found and asks you to confirm. You stay in control of what's on your plate.

Completed tasks get marked done with a date, and periodically you move them to Archive/ to keep Active.md focused.

## Projects and People

As you use the system, two directories grow naturally: Projects/ and Knowledge/People/.

**Projects/** holds one file per active project. Each file has a brief description, key contacts, current status, and notes from recent activity. When the agent scans your email or Slack and finds something relevant to a project, it updates the appropriate file. This gives you a running log without having to maintain it manually.

**Knowledge/People/** is a contact directory. When someone new shows up in an email, meeting invite, or Slack thread, the agent checks if they have an entry. If not, it creates one with whatever context is available (name, role, organization, how they're connected to you). Over time, these entries accumulate useful context: what projects someone works on, when you last interacted, communication preferences.

Both of these start sparse and fill in over time. You don't need to sit down and populate them upfront. The morning routine does it incrementally.

## Reminders

Drop a date-prefixed file in Knowledge/Reminders/:

```
2026-03-15-dentist-appointment.md
```

The file content can include details, checklists, or just a one-liner. During the morning routine, the agent scans for any reminders with today's date (or dates in the past that haven't been handled yet) and surfaces them in the briefing.

This is useful for things that aren't tasks but need to pop up on a specific day: appointments, follow-up windows, recurring personal reminders, or time-sensitive context you'll need later.

## End-of-Day Routine

This one is optional. The morning routine is the essential habit; the end-of-day routine is a nice-to-have that keeps things tidy.

Say "run the end-of-day routine" and the agent:

1. Asks you what happened today that hasn't been captured yet.
2. Checks Inbox/ for any notes you dropped in during the day.
3. Reconciles your task list with what got done.
4. Previews tomorrow's calendar so you're not surprised in the morning.

It should take two to five minutes. If you skip it, the morning routine catches everything the next day. But doing it means your morning starts cleaner and faster.

## Weekly Review

At the end of the week, ask the agent for a weekly summary. It creates a file in Weekly/ covering what got done, what slipped, what's coming next week, and any patterns worth noting.

This is also a good time to ask the agent to run vault maintenance: checking for broken links between files, flagging tasks that haven't been updated in two weeks, and making sure the dashboard matches reality. You can automate this by adding it to your instruction file as a weekly add-on, similar to how the morning routine works.

## The Feedback Loop

The most important thing to understand about Jesse is that it improves through use. The instruction file isn't something you write once and leave alone.

When the agent misclassifies an email as low priority, you correct it, and then you add a rule to the instruction file: "Emails from this client are always at least yellow." When a triage decision doesn't match your judgment, you refine the criteria. When you realize you want birthday reminders or a weekly status update draft, you add a recipe.

After a week or two of daily use, the system matches your judgment more often. That's because your instruction file has become more specific about your priorities and preferences.

A few practical tips:

- **Start simple.** The template instruction file is enough to get going. Add complexity only when you feel the need.
- **Correct out loud.** When the agent gets something wrong, tell it why. Then update the instruction file so it doesn't happen again.
- **Use Inbox/ liberally.** Any time you think "I need to remember to do X," drop a note. The system handles the rest.
- **Don't over-organize early.** A slightly messy vault that you actually use beats a pristine structure you avoid touching.
- **Read the recipes.** They're patterns extracted from real usage, and you can adopt them piecemeal as they become relevant to your workflow.

## What This Looks Like After a Few Weeks

After daily use, your vault will have:

- A Dashboard.md that accurately reflects your current priorities, updated every morning.
- A Today.md you can glance at on your phone to know what's next.
- Project files with running context you didn't have to manually maintain.
- People files that give you instant context before any meeting or message.
- A Drafts/ folder where you review messages before they go out.
- A Weekly/ archive showing your work patterns over time.
- An instruction file that's become genuinely personalized to how you think and work.

None of this requires a big upfront investment. It builds up session by session, correction by correction.
