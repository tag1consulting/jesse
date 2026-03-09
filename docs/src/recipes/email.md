# Email Integration

Scan your email inbox during the morning routine. Extract action items, flag replies needed, and surface anything time-sensitive.

## Prerequisites

An MCP connector for your email provider. Current options:

- **Gmail:** Available as a Cowork connector. Provides search, read message, read thread.
- **Fastmail / JMAP:** Via [`@jahfer/jmap-mcp-server`](https://github.com/nicholasareed/jmap-mcp-server) (npm package). Read-only.
- **Other providers:** If your provider supports IMAP, you may find community MCP servers. Check the [MCP server directory](https://github.com/modelcontextprotocol/servers).

## Configuration

The daily routine in `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md` already includes generic email scanning and TODO extraction steps. To customize for your setup, edit that file's Gather phase with your specific email address:

```markdown
- **Scan email** -- Check your-email@example.com for action items,
  replies, and updates. Scan all messages (read and unread) -- if it's
  still in the inbox, it's not done.
```

The Process phase already includes a TODO extraction step that covers email.

## The "Still in Inbox" Rule

The simplest email triage rule: if a message is still in your inbox, it hasn't been handled. This means you need to archive or move emails once you've dealt with them (in your email client, not in the vault). Jesse treats every inbox message as potentially actionable.

## Multiple Email Accounts

If you have separate work and personal email, add a bullet for each in the Gather phase of `Knowledge/Jesse-Guidelines/Start-of-Day-Routine.md`:

```markdown
- **Scan work email** -- Check work@company.com for action items.
- **Scan personal email** -- Check personal@example.com for bills,
  appointments, and anything time-sensitive.
```

Personal email scanning often surfaces things like: utility bills, medical appointment reminders, subscription renewals, shipping notifications, and school communications.

## What Jesse Does With Email

During the morning routine, Jesse:

1. Reads recent inbox messages
2. Identifies action items (things that need a response or a task)
3. Creates TODO entries in the appropriate project files and Dashboard.md
4. Flags anything time-sensitive in the morning briefing
5. Notes relevant context for today's meetings

Jesse does NOT send, reply to, or archive emails. That's your job. Jesse reads and extracts.

## Tips and Gotchas

**Keep your inbox manageable.** If you have thousands of unread emails, Jesse will waste time scanning irrelevant messages. Archive what's done. The system works best when your inbox is a genuine queue of unprocessed items.

**Be specific about what matters.** If you get a lot of newsletters or automated notifications, tell Jesse to skip them:

```markdown
When scanning email, skip: newsletters, automated notifications from
GitHub/Jira/etc, marketing emails. Focus on messages from people
that need a response or contain action items.
```

**The Fastmail JMAP connector has rough edges.** Jeremy found and fixed several bugs in the existing npm package and submitted a PR upstream. If you use Fastmail, expect some iteration.
