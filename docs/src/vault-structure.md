# Vault Structure

The vault is a directory of markdown files. Every file is plain text. You can read and edit everything from any markdown editor or from the command line.

## Directory Layout

```
Dashboard.md              Priority-sorted TODO index
Today.md                  Living daily task list (rebuilt each morning)
JESSE.md                  The instruction file

Inbox/                    Quick capture from phone/desktop
  archive/                Processed inbox notes

Projects/                 Source of truth per topic (one file per project)
  drafts/                 Active draft communications
    archive/              Sent drafts (date-prefixed)
      old/                Sent drafts older than 90 days

Knowledge/                Long-term reference material
  People/                 Contact directory
    Tag1/                 Your organization (rename for yours)
    Client/               Client contacts
    Vendor/               External service providers
    Candidate/            Hiring candidates
    Other/                Everyone else
  Reminders/              Date-based reminders
```

## Key Files

### Dashboard.md

The master TODO list. Every item is sorted into one of four sections:

- **Urgent** -- needs attention today
- **This Week** -- committed work for the current week
- **Waiting** -- blocked on someone else or a future date
- **Backlog** -- important but not time-sensitive

Every item has a timestamp: `(Added YYYY-MM-DD)` when created, `(Added YYYY-MM-DD, updated YYYY-MM-DD)` when modified. This prevents stale items from hiding in plain sight.

### Today.md

A living daily task list. Not a journal, not a log. It gets rebuilt each morning with today's schedule, tasks pulled from Dashboard.md, and new items from email and messaging. Structure: schedule at top, tasks grouped by time block, "Done" section at bottom for today's completions.

### JESSE.md (or CLAUDE.md)

The instruction file. This is where you define who you are, what your daily routine looks like, what tools are connected, and the rules the agent follows. See [The Instruction File](./instruction-file.md) for details.

## Conventions

### Naming

Filenames use `Hyphenated-Title-Case.md`. No spaces (they break shell commands and complicate link completion). Archive files get a date prefix: `YYYY-MM-DD-descriptive-name.md`.

### Wiki-links

Use full paths from the vault root: `[[Projects/Insurance]]`, `[[Knowledge/People/Vendor/Jane-Smith]]`. Bare filenames like `[[Insurance]]` break the first time you move a file.

### Markdown Only

Everything in the vault is markdown. No Word docs, no HTML, no PDFs. If something needs to be in another format, it lives outside the vault and gets linked.

## The Archive Pattern

The vault uses `archive/` subdirectories wherever items age out. Files move to archive, they don't get deleted. This is a design choice: you can always go back and check exact wording, and cleanup happens on your schedule, not automatically.

| Location | What goes there |
|---|---|
| `Inbox/archive/` | Processed inbox notes |
| `Projects/drafts/archive/` | Sent drafts (date-prefixed) |
| `Projects/drafts/archive/old/` | Drafts older than 90 days |
