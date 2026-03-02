# FAQ

## Do I need Obsidian?

No. Obsidian is convenient for wiki-link navigation and mobile sync, but the vault is just markdown files in directories. VS Code, Logseq, Typora, vim, or any text editor works. If Obsidian disappeared tomorrow, nothing would break except the convenience of tapping links on your phone.

## Do I need Claude or Cowork?

No. Jesse is designed to work with any AI agent that can read files, write files, and call tools. Claude's Cowork mode is what the author uses, but the instruction file and vault structure are agent-agnostic. If you're using Cowork, rename `JESSE.md` to `CLAUDE.md` so it loads automatically.

## How much does this cost?

The vault is free. Obsidian is free. Obsidian Sync is ~$4/month if you want mobile access (optional). The AI agent has whatever cost your provider charges. MCP connectors are generally free and open source.

## Is my data sent to the cloud?

The vault lives on your computer. During an AI session, the contents of files the agent reads are sent to your AI provider for processing. Between sessions, nothing is transmitted. The vault-as-memory model means you retain the knowledge, not the AI provider.

## How do I handle sensitive information?

Don't put passwords, API keys, or financial account numbers in the vault. For sensitive projects, you can exclude specific files from the agent's access or keep them in a separate directory. The instruction file can include rules about what the agent should skip.

## What if the AI makes a mistake?

The vault is deterministic. If the agent writes something wrong, you can see exactly what changed and fix it. This is one of the advantages of files over a database: you can review diffs, revert changes, and maintain version history with git.

The "show your judgment calls" principle also helps. When the agent tells you what it decided, you can catch mistakes before they propagate.

## How long before it's useful?

The first session. Even a minimal instruction file produces something useful: a scan of your inbox, a Today.md with your schedule, a morning briefing. It gets significantly better after a few days of iteration as you refine the instruction file for your specific situation.

## Can I use this with a team?

Jesse is designed for individual use. Each person has their own vault and instruction file. That said, there's no technical reason you couldn't share a vault structure or instruction file template across a team, with each person customizing it for their role.
