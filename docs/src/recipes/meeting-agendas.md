# Meeting Agendas

Auto-generate and maintain agenda drafts for every meeting on your calendar.

## The Problem

Walking into meetings unprepared wastes everyone's time. Manually building agendas means pulling context from multiple sources -- calendar, email, project files, past meetings -- and the preparation often doesn't happen.

## The Solution

Jesse generates agenda drafts during the morning routine and keeps them updated as new information arrives throughout the day.

## Vault Structure

```
Knowledge/
  Jesse-Guidelines/
    Meeting-Agenda-Guidelines.md    # Meeting agenda generation and update rules
Projects/
  drafts/                         # Active agenda drafts (auto-generated daily)
    archive/                      # Past agendas (reference for recurring meetings)
```

## Configuration

The template already includes meeting agendas. JESSE.md has a one-line pointer in the Rules section:

```markdown
### Meeting Agendas

Follow [[Knowledge/Jesse-Guidelines/Meeting-Agenda-Guidelines]] when preparing meeting agendas.
```

The daily routine in `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md` includes the agenda preparation step in Phase 3: Produce, which generates or updates agenda drafts for each meeting and gates the rest of the routine on their completion.

## The Guidelines File

The template includes this file at `Knowledge/Jesse-Guidelines/Meeting-Agenda-Guidelines.md`:

````markdown
# Meeting Agenda Guidelines

During the start-of-day routine, after scanning email, messaging, and calendar, generate an agenda draft for every meeting on today's calendar where you are a participant and the meeting involves discussion. Skip all-hands, company-wide broadcasts, or meetings where you're just an observer. Each agenda goes in `Projects/drafts/` as `YYYY-MM-DD-HHMM-meeting-name-agenda.md`, where HHMM is the meeting's start time in 24-hour format with no colon (colons are invalid in macOS/APFS filenames). Examples: `2026-03-05-1130-fabian-1on1-agenda.md` (11:30 AM), `2026-03-05-1500-client-intro-agenda.md` (3:00 PM). If a meeting's time is unknown when drafting, use `0000` as the HHMM segment (e.g., `2026-03-05-0000-unknown-time-agenda.md`) and flag it in Today.md so the time gets resolved. If an agenda draft already exists for a meeting -- from a previous session or manual creation -- read it first and update it rather than overwriting.

For each meeting, gather context before writing. Check the People KB for entries on each attendee -- pull up their Current Topics section. Check Dashboard.md and project files for open items relevant to the attendees. Look at past agendas for recurring meetings (search `Projects/drafts/archive/` for similar filenames) to understand the expected structure and cadence. If the meeting has a pattern -- weekly 1:1, ops standup, project review -- follow the established format. If it's a new or ad-hoc meeting, structure it as: context/purpose at top, discussion items, decisions needed, and any FYI items.

Keep agendas short and scannable. Lead with the most important or time-sensitive items. Each item should be one or two lines max -- enough to know what to discuss, not a briefing document. Link to project files or KB entries for background rather than inlining it. If there are items that need a decision, flag them clearly so they don't get lost in discussion. End with a section for items that can be mentioned quickly or deferred if time runs short.

Agendas are living documents until the meeting starts. When new information arrives during the day -- an email reply, a Slack message, a completed task -- update the relevant agenda draft. If a new issue surfaces that's relevant to an upcoming meeting, add it. If something gets resolved before the meeting, note that it's resolved rather than removing it (the attendee may want to know). The goal is that when the meeting starts, the agenda reflects everything known as of that moment.

Once the meeting is over, the agenda draft gets archived like any other draft: extract key decisions and action items into the relevant project files, then move to `Projects/drafts/archive/` with the date prefix. For recurring meetings, the archived agendas become the reference for building next week's agenda.

This file defines how agendas work in your vault. Change the structure, the level of detail, which meetings get agendas, and how context is gathered. Some people want detailed briefing notes with background on every topic. Some want three bullet points. Some want agendas only for 1:1s, not group meetings. Edit this file to match your meeting style.
````

## How It Works

1. During the morning routine, Jesse checks today's calendar and identifies meetings that need agendas.
2. For each meeting, Jesse gathers context: attendee KB entries, relevant project files, open items from Dashboard.md, past agendas for recurring meetings.
3. Agenda drafts land in `Projects/drafts/` and show up as links in Today.md.
4. As new information arrives during the day, Jesse updates the relevant agenda drafts.

## Tips and Gotchas

**Past agendas are the best template.** For recurring meetings, Jesse checks the archive for the previous agenda and follows the same structure.

**Agendas are drafts, not meeting notes.** They get archived after the meeting via the draft lifecycle. Decisions and action items go into project files.

**Not every meeting needs an agenda.** The guidelines file controls which meetings qualify. Edit it to skip all-hands, broadcasts, or meetings where you're just listening.

**Live updates matter.** An agenda that was accurate at 8am but stale by 2pm defeats the purpose. Jesse updates drafts as context changes.

**The guidelines file is yours to edit.** Different meeting culture, different structure needs, different level of detail -- change it directly.
