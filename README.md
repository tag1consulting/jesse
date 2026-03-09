# Jesse

An AI assistant built from folders of text files, an instruction manual, and no vendor lock-in.

Jesse pairs a sandboxed desktop AI agent with a structured vault of interconnected markdown files. A single instruction file (`JESSE.md`) tells the agent who you are, what your priorities look like, and exactly what to do when you start a session. The vault is your memory. The instruction file is your process. The AI is the engine.

**Read the full guide at [jesse.help](https://jesse.help).**

## Quick Start

1. Copy the `template/` directory to wherever you want your vault:

   ```bash
   cp -r template/ ~/jesse-vault/
   ```

2. Open the vault in [Obsidian](https://obsidian.md/) (free) or any markdown editor.

3. Edit `JESSE.md` for your situation: your name, your tools, your priorities. If you're using Claude's Cowork mode, rename it to `CLAUDE.md`.

4. Point your AI agent at the folder and ask it to run the start-of-day routine.

5. Iterate. The instruction file is a living document. Improve it every time something goes wrong or you learn a better way.

## What's in the Template

```
template/
├── JESSE.md              # The instruction file (start here)
├── Dashboard.md          # Priority-sorted TODO index
├── Today.md              # Living daily task list
├── Inbox/                # Quick capture from phone/desktop
├── Projects/
│   ├── Research/          # Completed research output
│   └── drafts/           # Active draft communications
│       └── archive/      # Sent drafts (date-prefixed)
└── Knowledge/
    ├── First-Run-Setup.md            # One-time vault setup procedure
    ├── Start-of-Day-Routine.md       # Daily routine orchestration
    ├── Weekly-Vault-Maintenance.md   # Weekly vault cleanup tasks
    ├── People-KB-Guidelines.md       # People entry format and rules
    ├── Research-Guidelines.md        # Research output standards
    ├── Writing-Voice-Guidelines.md   # Writing voice checklist
    ├── Meeting-Agenda-Guidelines.md  # Meeting agenda rules
    ├── People/           # Contact directory
    │   ├── Tag1/         # (your org — rename this)
    │   ├── Client/
    │   ├── Vendor/
    │   ├── Candidate/
    │   └── Other/
    └── Reminders/        # Date-based reminders
```

## Recipes

Recipes are community-contributed guides for setting up specific capabilities: email integration, WhatsApp scanning, birthday reminders, network health monitoring, and more. Browse them at [jesse.help/recipes](https://jesse.help/recipes/).

Want to contribute a recipe? See [CONTRIBUTING.md](CONTRIBUTING.md).

## Not Locked In

Jesse is not locked to Claude, Cowork, or Obsidian. The instruction file is a markdown document. The vault is a folder of markdown files. Any AI agent with file access and tool-calling can run it. Any markdown editor can view it.

Read more about the design philosophy in the [blog post](https://www.tag1.com/blog/building-my-ai-assistant/) or at [jesse.help](https://jesse.help).

## License

[MIT](LICENSE)
