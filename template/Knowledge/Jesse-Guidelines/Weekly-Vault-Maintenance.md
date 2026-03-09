# Weekly Vault Maintenance

Run once per week on a consistent day. Announce that you're running maintenance at the start.

- **Broken wiki-links** -- Scan all `.md` files for bare wiki-links (no path prefix), links to moved/renamed/deleted files. Fix all found.
- **Naming conventions** -- Find filenames with spaces or inconsistent casing. Rename to `Hyphenated-Title-Case.md`. Update all referencing wiki-links. Ignore `.gitkeep` files.
- **Archive date prefixes** -- Check `Projects/drafts/archive/` and `Inbox/archive/` for files missing `YYYY-MM-DD-` prefixes. Add them. Ignore `.gitkeep` files.
- **90-day archive purge** -- Move anything in `Projects/drafts/archive/` older than 90 days to `Projects/drafts/archive/old/`. Create the `old/` directory if it doesn't exist yet.
- **Orphan check** -- Look for Knowledge/ files with zero incoming links from Projects/ or Dashboard.md. Add cross-links where appropriate.
- **People KB consistency** -- No duplicate entries across subdirectories. New people mentioned in recent updates have KB entries.
- **Dashboard sync** -- No stale items marked urgent that are actually done, no active items missing.

Report a summary of what was found and fixed.

This file is yours to edit. Add maintenance tasks specific to your vault, remove ones that don't apply, adjust the cadence. The instruction file just points here -- changing this file changes the behavior immediately.
