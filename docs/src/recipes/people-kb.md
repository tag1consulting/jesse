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

## JESSE.md Configuration

```markdown
### People Knowledge Base

When working on a task involving a specific person:
1. Check if they have an entry in Knowledge/People/ (search all subdirectories).
2. Create if missing -- at minimum: name, role/title, contact info,
   relationship, context from current interaction.
3. Update if exists -- new topics, status changes, contact details.
4. Categorize correctly: employees in YourOrg/, clients in Client/,
   service providers in Vendor/, hiring candidates in Candidate/,
   everyone else in Other/.
5. Never duplicate. One file per person. If someone changes category,
   move the file.
6. Cross-link to relevant project files.
```

## Entry Template

```markdown
# Full Name

One-line description of who they are.

**Contact:** [method and details]
**Role:** [title]

## Background
[Professional history, how you know them]

## Current Topics
[Active items involving this person, with links to project files]

## Related
[Cross-links to project files and other KB entries]
```

For your own organization, you might add fields like schedule, timezone, reports-to, and type (full-time vs. contractor).

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
