# Weekly Vault Maintenance

Run a weekly hygiene pass to catch broken links, enforce naming conventions, clean up archives, and keep the vault consistent.

## The Problem

As the vault grows, things drift. Files get renamed without updating links. Naming conventions slip. Archives accumulate. People entries get stale. Without periodic maintenance, the vault slowly becomes unreliable.

## The Solution

A weekly routine (we run ours on Wednesdays) that systematically checks and fixes common issues.

## Configuration

The template already includes weekly vault maintenance. JESSE.md has a one-line pointer:

```markdown
### Weekly Vault Maintenance

Run once per week on a consistent day. Read and follow [[Knowledge/Jesse-Guidelines/Weekly-Vault-Maintenance]].
```

The detailed checklist lives in `Knowledge/Jesse-Guidelines/Weekly-Vault-Maintenance.md`, which the agent loads when it's time to run maintenance. To customize which checks run or change the cadence, edit that file directly.

## What Each Check Does

### Broken Wiki-Links

The most common issue. Someone renames a file, and all the `[[links]]` pointing to it break silently. Jesse scans every `.md` file for wiki-links that don't resolve to an existing file and fixes them.

Also catches bare wiki-links like `[[Insurance]]` that should be `[[Projects/Insurance]]`. Full paths prevent breaks when files move.

### Naming Conventions

The vault uses `Hyphenated-Title-Case.md` everywhere. Spaces in filenames break shell commands and complicate Obsidian's link completion. Jesse finds violations and renames them, updating all referencing links.

### Archive Cleanup

Files in `Projects/drafts/archive/` should have date prefixes (`YYYY-MM-DD-filename.md`). Files older than 90 days move to `old/`. This keeps the archive scannable.

### Orphan Check

Knowledge files that nothing links to are probably stale or forgotten. Jesse finds them and either adds appropriate cross-links or flags them for your review.

### Dashboard Sync

Completed items still marked urgent. Active items missing from Dashboard.md. Stale timestamps. Jesse catches the drift between what's actually happening and what the dashboard shows.

## Tips and Gotchas

**Pick a quiet day.** Vault maintenance doesn't need to happen on the busiest day of your week. Wednesday works well as a mid-week reset.

**Let Jesse report what it found.** The maintenance summary tells you the health of your vault. If it's finding a lot of issues every week, something in your workflow needs adjustment.

**Don't skip it.** It's tempting to skip maintenance when you're busy. That's exactly when drift accelerates. The weekly pass takes Jesse a few minutes and saves you from a much bigger cleanup later.
