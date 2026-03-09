# Birthday and Date Reminders

Track birthdays, anniversaries, holidays, document expirations, and any recurring date with multi-stage reminders that surface in your morning briefing.

This recipe uses [Obsidian Reminders](https://github.com/uphy/obsidian-reminder), a free Obsidian plugin that triggers notifications based on a simple date syntax in your markdown files. Jesse reads these same files during the morning routine and surfaces upcoming reminders in the briefing.

## Prerequisites

- Obsidian with the [Reminders](https://github.com/uphy/obsidian-reminder) plugin installed
- The plugin's "Reminder Format" set to include `(@YYYY-MM-DD)` syntax

## Vault Structure

Create a `Knowledge/Reminders/` directory in your vault. Each category of reminders gets its own file:

```
Knowledge/Reminders/
├── Family-Dates.md           # Birthdays, anniversaries, personal dates
├── Company-Holidays.md       # Your org's holiday calendar
├── School-Holidays.md        # If you have kids in school
├── Document-Expirations.md   # Passports, IDs, car inspections
└── DST-Transitions.md        # Daylight saving time changes (if international)
```

## How Reminders Work

The Obsidian Reminders plugin watches for this syntax:

```markdown
- [ ] Reminder text (@YYYY-MM-DD)
- [ ] Reminder with time (@YYYY-MM-DD HH:MM)
```

When the date arrives, Obsidian shows a notification. When you check the box, the reminder is done.

Jesse reads these files during the morning routine (Phase 1: Gather) and includes any unchecked reminders matching today or this week in the briefing.

## Multi-Stage Reminders

The real power is in setting up multiple reminders per event at different lead times. For a birthday:

```markdown
### Mom's Birthday (Aug 12 -- turning 80)
- [ ] Mom's birthday in 1 month -- turning 80! Plan something special? (@2026-07-13)
- [ ] Mom's birthday in 1 week -- turning 80 (@2026-08-05)
- [ ] Mom's birthday tomorrow -- turning 80 (@2026-08-11)
- [ ] Happy 80th birthday Mom! (@2026-08-12)
```

The 1-month reminder gives you time to plan. The 1-week reminder is a nudge. The day-before is your last chance. The day-of is a greeting prompt.

For less significant dates, two stages might be enough:

```markdown
### Memorial Day (May 25)
- [ ] Company closed May 25 (Memorial Day) -- 1 week reminder (@2026-05-18)
- [ ] Company closed tomorrow (Memorial Day) (@2026-05-24)
```

## Configuration

The daily routine in `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md` already includes a reminder scanning step in the Gather phase:

```markdown
- **Read all files in Knowledge/Reminders/** -- Find unchecked reminders
  (`- [ ]`) with dates matching today or this week.
```

No additional configuration needed. Just create your reminder files in `Knowledge/Reminders/`.

## Example: Family Dates File

A complete family dates file includes a reference table and reminders:

```markdown
# Family Dates and Reminders

## Birthdays

| Who | Date | Age This Year |
|-----|------|---------------|
| Mom | August 12 | 80 |
| Dad | September 18 | 81 |
| Partner | June 29 | 46 |
| Kid 1 | April 11 | 9 |
| Kid 2 | April 21 | 11 |

## Anniversaries

| Date | What | Years |
|------|------|-------|
| June 19 | Wedding anniversary | 16 |

## Key Dates

| Date | What | Notes |
|------|------|-------|
| Mar 8 | International Women's Day | Big in Italy. Flowers. |
| Mar 19 | Father's Day (Italy) | San Giuseppe |

## Reminders

### Kid 1's Birthday (Apr 11 -- turning 9)
- [ ] Birthday in 1 month -- turning 9, start planning (@2026-03-12)
- [ ] Birthday in 1 week (@2026-04-04)
- [ ] Birthday tomorrow -- turning 9 (@2026-04-10)
- [ ] Happy 9th birthday! (@2026-04-11)
```

## Document Expiration Tracking

The same pattern works for documents that expire:

```markdown
# Document Expirations

## Passports
| Who | Passport # | Issued | Expires | Renew By |
|-----|-----------|--------|---------|----------|
| You | AB123456 | 2020-03-15 | 2030-03-15 | 2029-09-15 |

## Reminders

### Your Passport (expires 2030-03-15)
- [ ] Passport expires in 6 months -- start renewal (@2029-09-15)
- [ ] Passport expires in 3 months (@2029-12-15)
- [ ] Passport expires in 1 month -- urgent (@2030-02-15)
```

## DST Transition Tracking

If you work across time zones, DST transitions cause meeting offsets. Track them:

```markdown
### US Spring Forward (Mar 8)
- [ ] US clocks spring forward Sunday. Meetings with US team shift
      1hr for 3 weeks until EU changes Mar 29. (@2026-03-05)
- [ ] US springs forward tomorrow. Expect meeting time shifts. (@2026-03-07)
```

## Tips and Gotchas

**Set reminders when you create the event, not later.** The whole point is that you set them up once and forget about them until they surface.

**Use the reference tables.** The tables at the top of each file make it easy to look up dates without scrolling through reminders. Jesse also uses them to calculate ages and milestones.

**Annual refresh.** At the start of each year, ask Jesse to generate next year's reminders from the reference tables. It takes about 30 seconds.

**Check off reminders when you see them.** Obsidian Reminders will keep nagging until you check the box. Jesse won't re-surface checked items.

**Keep it in Knowledge/Reminders/, not in project files.** Reminders are reference data, not TODO items. They feed into Dashboard.md and Today.md during the morning routine, but they live separately.
