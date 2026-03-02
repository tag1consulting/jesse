# Draft Lifecycle

Manage active drafts, archive sent communications, and keep a permanent record of key details in your project files.

## The Problem

Draft emails and messages accumulate across multiple accounts and apps. Without a system, you end up with a graveyard of half-written drafts in Gmail, a Google Doc you'll never organize, and no record of what you actually sent.

## The Solution

All drafts live in one place: `Projects/drafts/`. When something gets sent, key details are extracted into the project file and the draft is archived. The project file is the permanent record. The archive is a short-term safety net.

## JESSE.md Configuration

```markdown
### Draft Lifecycle

`Projects/drafts/` is for any document that needs review before it goes
out -- not just emails. This includes checklists, meeting agendas, talking
points, and anything meant for an audience beyond yourself.

Once a draft is sent or delivered:
1. Extract key details into the relevant project file (e.g., "Sent
   2026-02-12: proposed X, asked for Y by Z date").
2. Move to `Projects/drafts/archive/` with date prefix:
   `YYYY-MM-DD-original-filename.md`.
3. Purge after 90 days -- move to `Projects/drafts/archive/old/`.
```

## Vault Structure

```
Projects/
  drafts/                   # Active drafts (things not yet sent)
    archive/                # Sent drafts (date-prefixed)
      old/                  # Sent drafts older than 90 days
```

## How It Works

1. You ask Jesse to draft something: "Draft a reply to the insurance broker about the renewal terms."
2. Jesse creates a file in `Projects/drafts/` with the draft content.
3. You open it in Obsidian, review and edit it, then send it yourself from your email client.
4. You tell Jesse it's been sent. Jesse extracts the key details into the project file and moves the draft to archive.

## What Goes in Drafts

Anything that's going to be shared, discussed, or handed to someone:

- Email replies and outreach
- Meeting agendas and talking points
- Checklists for vendors or contractors
- Notes for external meetings
- Anything you want to review before it goes out

## Tips and Gotchas

**Jesse never sends anything.** The draft workflow is deliberate. You review, you edit, you send. Jesse drafts and archives.

**Keep drafts short.** A draft that needs a paragraph is better than one that needs a page. Steer rather than detail. You'll edit it anyway.

**The project file is the permanent record.** Don't rely on the archive for long-term reference. The extracted details in the project file should capture what was sent, to whom, and what was proposed or agreed.

**Clean up the archive periodically.** The 90-day move to `old/` keeps the archive manageable. Jeremy purges `old/` manually from Obsidian when inclined.
