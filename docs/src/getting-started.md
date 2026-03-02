# Quick Start

You can have Jesse running in about 10 minutes.

## 1. Create Your Vault

Clone this repo and copy the template directory:

```bash
git clone https://github.com/tag1consulting/jesse.git
cp -r jesse/template/ ~/jesse-vault/
```

Or just create the directory structure by hand. It's just folders and markdown files.

## 2. Choose Your Editor

[Obsidian](https://obsidian.md/) is free and works well for this. Its `[[wiki-links]]` turn your vault into a navigable knowledge base, and it syncs to your phone if you pay for Obsidian Sync (~$4/month). But any markdown editor works. VS Code, Logseq, Typora, or `vim` in a terminal. The vault is just files.

If you use Obsidian, open your vault directory as a new vault. Install the [Reminders](https://github.com/uphy/obsidian-reminder) plugin if you want date-based reminders (see the [reminders recipe](./recipes/reminders.md)).

## 3. Edit the Instruction File

Open `JESSE.md` in your vault. This is the file that tells the AI agent what to do. The template has a working starter configuration, but you need to customize it:

- Replace the placeholder name and role with yours.
- Remove integrations you don't have (WhatsApp, Slack, etc.).
- Adjust the daily routine to match your workflow.
- Add your own preferences and rules.

If you're using Claude's Cowork mode, rename `JESSE.md` to `CLAUDE.md`. Cowork picks it up automatically.

## 4. Connect Your AI Agent

Open your AI agent of choice and point it at your vault directory.

**Claude Cowork:** Open Cowork, select your vault folder. `CLAUDE.md` loads automatically.

**Claude Code:** Run `claude` from your vault directory. It reads `CLAUDE.md` as project instructions.

**Other agents:** Any agent with file access and tool-calling capabilities can use the instruction file. Point it at the vault and include the contents of `JESSE.md` in your system prompt or project instructions.

## 5. Run the Start-of-Day Routine

Ask the agent to run the start-of-day routine. On the first run it will:

- Read your (empty) inbox
- Try to scan email and messaging (skip what's not connected)
- Build `Today.md` and `Dashboard.md`
- Deliver a morning briefing (which will be short since everything is new)

## 6. Iterate

The first session won't be perfect. The instruction file will have rules that are ambiguous for your situation, and the agent will make choices you'd make differently. That's expected.

Work with the agent to tighten the instruction file. Add rules for the situations you encounter. Within a few days you'll have something that genuinely reduces the overhead of managing a complex workload.

The instruction file is a living document. Revise it every time something goes wrong or you learn a better way. Don't try to build the whole thing on day one.

## What's Next

- Read about the [instruction file](./instruction-file.md) in detail.
- Understand the [vault structure](./vault-structure.md).
- Browse [recipes](./recipes/index.md) for specific capabilities to add.
