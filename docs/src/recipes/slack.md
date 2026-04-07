# Slack Integration

Scan Slack channels, DMs, and mentions for action items during the morning routine. This recipe addresses common pitfalls with the Slack MCP connector -- particularly stale data in channel scans and unreliable DM coverage -- using timestamp-gated reads and a two-pronged DM strategy.

## Prerequisites

A Slack MCP connector. Current options:

- **Claude Cowork:** Built-in Slack connector. Provides channel reads, thread reads, search, and user profile lookup.
- **Community MCP servers:** Check the [MCP server directory](https://github.com/modelcontextprotocol/servers) for alternatives.

Your connector needs at minimum: `read_channel` (with timestamp filtering), `search` (with date modifiers), and `read_thread`.

> **Parameter naming:** This recipe uses `oldest` as the timestamp filter parameter on channel reads, which matches the Slack Web API's `conversations.history` endpoint and the Claude Cowork connector. Community MCP servers may use different parameter names (e.g., `after`, `since`, `start_time`). Check your connector's documentation and adjust the vault file accordingly.

## Configuration

### 1. Create a Slack config section in your instruction file

Add a section listing your priority channels and frequent DM contacts. This is the data the scanning routine uses -- keep it updated as your channels and contacts change.

```markdown
## Slack

### Priority Channels

Scan in this order during the morning routine:

| Priority | Channel | ID | Notes |
|----------|---------|-----|-------|
| 1 | #leadership | `CXXXXXXXXXX` | Highest priority |
| 2 | #operations | `CXXXXXXXXXX` | |
| 3 | #finance | `CXXXXXXXXXX` | Always scan even without @mentions |
| 4 | #access-requests | `CXXXXXXXXXX` | Flag pending requests |

### Frequent DM Contacts

Read these DM channels directly (search alone can miss DMs):

| Contact | DM Channel ID |
|---------|--------------|
| Alice | `DXXXXXXXXXX` |
| Bob | `DXXXXXXXXXX` |

> To find a DM channel ID: open a DM with the person, then look at the
> URL in Slack -- the `DXXXXXXXXXX` portion is the channel ID. Or use
> `slack_read_channel` with their user ID.
```

### 2. Add Slack scanning to the daily routine

Replace or extend the "Scan messaging" step in `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md`:

```markdown
- **Scan Slack** -- Use timestamp-gated reads. See
  [[Knowledge/Jesse-Guidelines/Slack-Scanning-Guidelines]] for the
  full approach. Key points:
  - Calculate scan window from Dashboard.md's "Last updated" date
    minus a 4-hour overlap buffer. Default to 96 hours if no date.
  - Use `slack_read_channel` with the `oldest` timestamp for each
    priority channel (listed in instruction file).
  - Two-pronged DM scan: `to:me after:YYYY-MM-DD` search PLUS
    direct reads of known DM channels.
  - Dedup against Dashboard.md's Slack section before surfacing.
```

### 3. Create the detailed guidelines file

Create `Knowledge/Jesse-Guidelines/Slack-Scanning-Guidelines.md` in your vault with the content from the [Vault File](#vault-file) section below.

## Vault Structure

One new file:

```
Knowledge/Jesse-Guidelines/Slack-Scanning-Guidelines.md
```

No new directories needed.

## How It Works

### The Problem

Slack MCP connectors have two reliability issues that cause bad morning briefings:

1. **Stale data in channel scans.** Broad search queries (or channel reads without timestamp filters) return a mix of old and new messages. Without context about what was already surfaced in previous sessions, the agent presents week-old discussions as if they're new. This wastes time and erodes trust.

2. **Missed DMs.** The `to:me` search modifier is unreliable -- it can miss DMs entirely, especially from less frequent contacts. If DMs are your only scan method, you'll silently drop action items.

3. **Invisible thread replies.** `slack_read_channel` with an `oldest` timestamp only returns top-level messages posted after that timestamp. Thread replies on older parent messages -- even brand-new ones with @mentions -- are completely invisible to channel reads. If someone replies to a week-old thread with "@you please review this," the channel scan won't see it.

### The Solution

The vault file below implements two techniques to solve these problems:

**Timestamp-gated channel reads.** Instead of broad search queries, use `slack_read_channel` with an `oldest` timestamp derived from your Dashboard's last-updated date. This returns exactly the messages posted since your last session -- no stale data. The scan window adapts automatically: a normal weekday gets ~28 hours of lookback, a long weekend gets ~96 hours, because it's anchored to when you last ran the routine rather than a fixed window.

**Two-pronged DM scanning.** A `to:me` search catches DMs from anyone, but misses some messages. Direct reads of known DM channels never miss messages, but only cover contacts you've listed. Using both together eliminates the gap.

**Mention search for thread replies.** The `to:me` search that's already part of DM scanning (Prong 1) does double duty: it also catches @mentions in thread replies on older parent messages. The key is to check those search results for thread replies in channels (where `thread_ts` differs from `message_ts`), not just DMs. For any thread reply found, `slack_read_thread` pulls the full context. This closes the gap that channel reads alone can't cover.

The vault file also covers dedup against Dashboard.md (so tracked items don't resurface), thread context handling, and subagent delegation patterns. See the [Vault File](#vault-file) section for the complete implementation.

## Vault File

Create this file at `Knowledge/Jesse-Guidelines/Slack-Scanning-Guidelines.md`:

````markdown
# Slack Scanning Guidelines

Reliable Slack scanning during the morning routine. Use timestamp-gated
channel reads and two-pronged DM scanning to avoid stale data and missed
messages.

## Scan Window

1. Parse Dashboard.md's "Last updated" line for the previous session date.
2. Convert to Unix timestamp, subtract 4 hours (overlap buffer).
3. If no date available, default to 96 hours ago.
4. Use this timestamp as `oldest` on all `slack_read_channel` calls.

Compute the timestamp (replace YYYY, M, D, H with values parsed from
Dashboard.md's "Last updated" line):

```bash
python3 -c "
from datetime import datetime, timezone, timedelta
# Replace these with the actual date/time from Dashboard.md
last_session = datetime(YYYY, M, D, H, 0, 0, tzinfo=timezone.utc)
buffered = last_session - timedelta(hours=4)
print(f'{buffered.timestamp():.0f}')
"
```

## Channel Reads

For each priority channel (listed in instruction file), call:

```
slack_read_channel(
  channel_id="{ID}",
  oldest="{timestamp}",
  limit=50,
  response_format="concise"
)
```

If pagination is indicated, fetch the next page.

## DM Scanning (Two-Pronged)

### Prong 1: Search

```
slack_search_public_and_private(
  query="to:me after:YYYY-MM-DD",
  sort="timestamp",
  sort_dir="desc",
  limit=20,
  include_context=true
)
```

Use the previous session date minus 1 day.

### Prong 2: Direct DM Reads

For each frequent DM contact (listed in instruction file), call
`slack_read_channel` with their DM channel ID and the same `oldest`
timestamp.

Direct reads catch messages that `to:me` search misses.

## Thread Replies and @Mentions

### The gap: thread replies on older messages

`slack_read_channel` with `oldest` only returns **top-level channel
messages** posted after the timestamp. It does **not** return thread
replies on older parent messages, even if the reply itself is new.
This means @mentions in threads on older posts will be missed by
channel reads alone.

**Example:** You post a message on Monday. Someone replies in that
thread on Thursday with "@you can you review this?" The Thursday
channel scan (with `oldest` set to Wednesday) will NOT see this
reply because the parent message is from Monday.

### Fix: Add a mention search pass

After all channel reads and DM scans, run an additional search for
@mentions:

```
slack_search_public_and_private(
  query="to:me after:YYYY-MM-DD",
  sort="timestamp",
  sort_dir="desc",
  limit=20,
  include_context=true
)
```

This overlaps with the DM search (Prong 1) and can be combined into
a single search. The key point is that results must be checked for
**thread replies in channels**, not just DMs. For any result that is
a thread reply (`thread_ts` differs from `message_ts`), use
`slack_read_thread` to get full context before summarizing.

### Enriching channel read results

When a channel message has replies or someone tags you with a vague
message ("^", "+1", etc.), use `slack_read_thread` with the
`message_ts` to get full context before summarizing.

## Dedup

Before surfacing any item:

1. Check Dashboard.md Slack section for existing entries.
2. Same item + no state change = skip.
3. Same item + state change = surface with delta noted.

## Known Quirks

- `slack_search_channels` may not find Slack Connect or shared channels.
  Use `slack_search_public_and_private` with `in:channel-name` as
  fallback, or read by channel ID directly.
- `to:me` search can return empty results even when DMs exist. This is
  why Prong 2 (direct reads) exists.
- Empty search results don't mean the connector is broken. Verify with
  a `slack_read_channel` call on a known-active channel.
- Formatted dates in search results can appear shifted. Prefer the
  Unix `message_ts` for determining when a message was posted.
- Thread replies on old messages are invisible to channel reads.
  `slack_read_channel` with `oldest` only returns top-level messages.
  New thread replies (even with @mentions) on parent messages older
  than the scan window won't appear. The `to:me` mention search pass
  catches these.

## Maintaining the Contact List

When a new frequent DM contact is discovered, add their DM channel ID
to the instruction file's Frequent DM Contacts table. To find a DM
channel ID, use `slack_read_channel` with the person's user ID.
````

## Tips and Gotchas

**The `to:me` search is unreliable for DMs.** This was the single biggest source of missed items. The two-pronged approach (search + direct reads) eliminated the problem. If you only do one thing from this recipe, add direct DM channel reads.

**Don't use a fixed scan window.** A 24-hour window misses things after weekends. A 96-hour window surfaces too much stale data on weekdays. Anchoring to the Dashboard date adapts automatically.

**The 4-hour overlap buffer matters.** Without it, messages posted near the end of the previous session can fall through the crack between "last session ended" and "new session's oldest timestamp."

**Subagents need context to avoid the stale-data problem.** If you delegate Slack scanning to a subagent and don't pass the timestamp and Dashboard context, the subagent will use broad searches and present old messages as new. This is the failure mode that motivated this recipe.

**Keep your channel list and DM contacts list updated.** When you start monitoring a new channel or notice DMs from a new frequent contact, add them to the instruction file. The scanning routine only checks what's listed.

**Thread replies on old messages are invisible to channel reads.** This is the subtlest gap. Someone can @mention you in a thread on a week-old message and `slack_read_channel` won't return it, no matter what `oldest` is set to. The `to:me` mention search catches these -- make sure you're checking search results for thread replies (where `thread_ts` differs from `message_ts`) and pulling full thread context with `slack_read_thread`.

**Slack's "Save for Later" list has no API.** Slack retired the stars/reminders endpoints in 2023 when they launched "Save for Later." There is no way to read this list programmatically. Workaround: manually review your Later list and drop anything actionable into `Inbox/` or `Knowledge/Reminders/`.
