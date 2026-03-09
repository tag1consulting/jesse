# People Knowledge Base

Maintain an automatic contact directory that grows as you work. When someone new appears in a meeting, email, or message thread, Jesse creates an entry. When someone you know comes up again, their file gets updated with new context.

## The Problem

You interact with dozens of people across different contexts. Remembering who someone is, what you last discussed, and how to reach them gets harder as the number of contacts grows. This is especially true for people you interact with infrequently.

## The Solution

A `Knowledge/People/` directory with one markdown file per person, organized into categories. Jesse maintains it automatically as part of regular work.

## Vault Structure

```
Knowledge/People/
├── YourOrg/          # Your organization's employees/contractors
├── Client/           # Client contacts
├── Vendor/           # External service providers
├── Candidate/        # Hiring candidates
└── Other/            # Everyone else
```

Rename `YourOrg/` to match your organization.

## Configuration

The template already includes the People KB. JESSE.md has a one-line pointer in the Rules section:

```markdown
### People Knowledge Base

Follow [[Knowledge/Jesse-Guidelines/People-KB-Guidelines]] when creating or updating people entries in `Knowledge/People/`.
```

The detailed rules and entry template live in `Knowledge/Jesse-Guidelines/People-KB-Guidelines.md`, which the agent loads whenever it needs to create or update a person entry. To customize the entry format (add timezone, pronouns, reports-to, etc.), edit that file directly.

## How It Works

Jesse checks the People directory whenever a name comes up during a task:

- **New person in an email thread?** Jesse creates an entry with whatever context is available.
- **Meeting prep for a 1:1?** Jesse pulls up their entry and includes current topics in the briefing.
- **Someone's status changes?** Jesse updates their entry (new role, new project, left the company).

Over time, this builds a contact database you actually use. Pull it up during calls to remember context. Search it when you can't remember someone's email.

## Tips and Gotchas

**One file per person, no exceptions.** Duplicates cause confusion. If someone changes roles (vendor becomes employee), move the file to the new directory.

**Cross-link generously.** Every people entry should link to relevant project files, and project files should link back to people. This makes it easy to navigate from a project to the people involved and vice versa.

**Don't over-document.** The entry should contain enough to jog your memory, not a biography. Name, role, contact info, current topics, and a few lines of background.

**The directory grows organically.** Don't try to populate it all at once. It fills in naturally as Jesse encounters people during daily work. After a few months, you'll have a surprisingly comprehensive directory.
