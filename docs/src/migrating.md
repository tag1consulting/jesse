# Migrating from Earlier Versions

If you set up your vault before the Knowledge/Jesse-Guidelines/ restructure, your JESSE.md probably contains inline routines (numbered step-by-step procedures for the daily routine, weekly maintenance, first-run setup, and people KB rules) and your guideline files (Research-Guidelines.md, Writing-Voice-Guidelines.md, Meeting-Agenda-Guidelines.md) live directly under Knowledge/ rather than in a Jesse-Guidelines/ subdirectory.

The new structure moves all agent behavior rules into `Knowledge/Jesse-Guidelines/` and slims JESSE.md down to short summaries with `[[wiki-link]]` pointers. This frees context window budget for actual work each session.

## What Changes

| Before | After |
|---|---|
| Daily routine inline in JESSE.md (~60 lines) | `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md` + 3-line summary in JESSE.md |
| Weekly maintenance inline in JESSE.md (~15 lines) | `Knowledge/Jesse-Guidelines/Weekly-Vault-Maintenance.md` + 1-line pointer in JESSE.md |
| First-run setup inline in JESSE.md (~40 lines) | `Knowledge/Jesse-Guidelines/First-Run-Setup.md` + 1-line pointer in JESSE.md |
| People KB rules inline in JESSE.md (~15 lines) | `Knowledge/Jesse-Guidelines/People-KB-Guidelines.md` + 1-line pointer in JESSE.md |
| `Knowledge/Research-Guidelines.md` | `Knowledge/Jesse-Guidelines/Research-Guidelines.md` |
| `Knowledge/Writing-Voice-Guidelines.md` | `Knowledge/Jesse-Guidelines/Writing-Voice-Guidelines.md` |
| `Knowledge/Meeting-Agenda-Guidelines.md` | `Knowledge/Jesse-Guidelines/Meeting-Agenda-Guidelines.md` |
| `Knowledge/People/Tag1/` | `Knowledge/People/YourOrg/` (or your org name) |
| Numbered lists for rules | Bold-keyword bullets |
| Context Management section absent | Context Management section added |

## Migration Prompt

Copy the prompt below and give it to your AI agent in your vault directory. It will walk through the migration step by step, preserving your customizations.

Before running: make sure your vault is backed up or version-controlled. The prompt asks the agent to move files and rewrite JESSE.md.

````
You are migrating this vault from the old Jesse format to the new restructured
format. The goal is to move all agent behavior rules into
Knowledge/Jesse-Guidelines/ and slim down JESSE.md to short summaries with
[[wiki-link]] pointers to those files.

Work through these steps in order. After each step, tell me what you did.

## Step 1: Create the directory

Create `Knowledge/Jesse-Guidelines/` if it doesn't already exist.

## Step 2: Move existing guideline files

If any of these files exist directly under Knowledge/, move them into
Knowledge/Jesse-Guidelines/:
- Knowledge/Research-Guidelines.md
- Knowledge/Writing-Voice-Guidelines.md
- Knowledge/Meeting-Agenda-Guidelines.md

After moving, scan the entire vault for wiki-links referencing the old paths
(e.g., [[Knowledge/Research-Guidelines]]) and update them to the new paths
(e.g., [[Knowledge/Jesse-Guidelines/Research-Guidelines]]).

## Step 3: Extract inline content from JESSE.md into new files

Read JESSE.md carefully. Look for these inline sections and extract them into
separate files. Preserve the content -- these are my customizations.

**Start-of-Day Routine:** If JESSE.md contains a detailed multi-step daily
routine (typically with Gather/Process/Produce phases and numbered steps),
extract it into `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md`. In
JESSE.md, replace the detailed steps with a short summary like:

```
### Start of Day

Run this at the start of each working session. Read and follow
[[Knowledge/Jesse-Guidelines/Start-of-Day-Routine]].

Three phases: **Gather** (inbox, email, calendar, messaging, dashboard,
reminders), **Process** (act on inbox instructions, extract TODOs, flag
meeting items, audit links), **Produce** (build Today.md, prepare agendas,
update dashboard, archive inbox, deliver briefing).

If `Dashboard.md` does not exist, run First Run instead.
```

**Weekly Vault Maintenance:** If JESSE.md has inline maintenance steps
(broken links, naming conventions, archive cleanup, etc.), extract them into
`Knowledge/Jesse-Guidelines/Weekly-Vault-Maintenance.md`. Replace with:

```
### Weekly Vault Maintenance

Run once per week on a consistent day. Read and follow
[[Knowledge/Jesse-Guidelines/Weekly-Vault-Maintenance]].
```

**First-Run Setup:** If JESSE.md has inline first-run/setup instructions,
extract them into `Knowledge/Jesse-Guidelines/First-Run-Setup.md`. Replace
with:

```
## First Run

If `Dashboard.md` does not exist in the vault root, this is a first-run
session. Do NOT attempt the Start of Day routine. Instead, read and follow
[[Knowledge/Jesse-Guidelines/First-Run-Setup]].
```

**People KB Rules:** If JESSE.md has inline rules for creating/updating
people entries (beyond a one-line pointer), extract them into
`Knowledge/Jesse-Guidelines/People-KB-Guidelines.md`. Replace with:

```
### People Knowledge Base

Follow [[Knowledge/Jesse-Guidelines/People-KB-Guidelines]] when creating or
updating people entries in `Knowledge/People/`.
```

## Step 4: Add Context Management section

If JESSE.md doesn't already have a Context Management section, add one after
Core Principles:

```
## Context Management

- **Load on demand.** Knowledge/ files linked from this document contain
  routines, guidelines, and reference material. Read them when the task calls
  for it, not preemptively.
- **Delegate verbose tools to subagents.** Messaging scans, large email
  inboxes, and research tasks can return large payloads. Delegate them to
  subagents that return summaries when the main session doesn't need the raw
  data.
- **Direct reads for known paths.** When you know which file you need, read
  it directly instead of scanning directories.
```

## Step 5: Convert numbered lists to bold-keyword bullets

Scan JESSE.md for numbered lists in the Rules section. Convert them to
bold-keyword bullet format. For example:

Before:
```
1. Extract key details into the relevant project file.
2. Move to `Projects/drafts/archive/` with date prefix.
3. Purge after 90 days.
```

After:
```
- **Extract** key details into the relevant project file.
- **Move** to `Projects/drafts/archive/` with date prefix.
- **Purge** after 90 days.
```

## Step 6: Update vault structure block

If JESSE.md has a Vault Structure section, update it to reflect the new
Knowledge/Jesse-Guidelines/ directory and list all the files that now live
there.

## Step 7: Update wiki-links vault-wide

Scan every .md file in the vault for wiki-links that reference the old
locations. Update them all. Common patterns:
- [[Knowledge/Research-Guidelines]] → [[Knowledge/Jesse-Guidelines/Research-Guidelines]]
- [[Knowledge/Writing-Voice-Guidelines]] → [[Knowledge/Jesse-Guidelines/Writing-Voice-Guidelines]]
- [[Knowledge/Meeting-Agenda-Guidelines]] → [[Knowledge/Jesse-Guidelines/Meeting-Agenda-Guidelines]]

## Step 8: Verify

After all changes:
1. List the contents of Knowledge/Jesse-Guidelines/ and confirm all expected
   files are there.
2. Scan the vault for any remaining references to the old paths.
3. Read JESSE.md back and confirm it's lean (under ~180 lines) with
   wiki-link pointers instead of inline procedures.
4. Tell me what you changed, what you preserved, and anything that looked
   unusual.
````

## Manual Migration

If you prefer to do it by hand:

1. **Get the new template.** Download or clone the latest [Jesse template](https://github.com/tag1consulting/jesse/tree/main/template).
2. **Compare JESSE.md files.** Open the new template's JESSE.md alongside yours. Merge section by section, keeping your customizations (Who I Am, Connected Tools, Preferences, any custom rules) but adopting the new structure (wiki-link pointers, Context Management section, slimmed-down routines).
3. **Create Knowledge/Jesse-Guidelines/.** Copy the 7 files from the template into your vault. Then review each one and merge in any customizations from your old inline content.
4. **Move guideline files.** If you have Research-Guidelines.md, Writing-Voice-Guidelines.md, or Meeting-Agenda-Guidelines.md directly under Knowledge/, move them into Jesse-Guidelines/ and update wiki-links.
5. **Update wiki-links.** Search your vault for `[[Knowledge/Research-Guidelines]]` and similar old paths. Update them all.
6. **Test.** Run a start-of-day session and verify the agent loads the routines from the new locations.
