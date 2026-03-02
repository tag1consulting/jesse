# Design Philosophy

## You Make the Decisions

Jesse surfaces information, flags priorities, drafts things for your review, and tracks what's open. It doesn't decide strategy, send emails, or make commitments on your behalf. The morning briefing is designed to require your judgment. The AI is a force multiplier, not a replacement for thinking.

## The Vault Is Your Memory

Each AI session starts fresh. The agent doesn't remember yesterday. But the vault does, and unlike an AI's memory, the vault is deterministic. Files only change when you change them or when Jesse does during a session you're directing. Nothing gets remembered or forgotten by accident.

Between sessions, you can open Obsidian on your phone, read through everything, rework drafts, fix priorities, all without AI involved. You can see exactly what the system knows.

## No Vendor Lock-in

Jesse is not locked to Claude, Cowork, or Obsidian.

The instruction file is a markdown document. The vault is a folder of markdown files. Any AI agent with file access and tool-calling can run it. Any markdown editor can view it. If you switch providers tomorrow, the vault comes with you.

Markdown files in a folder will outlast every note-taking app on the market.

## Safety by Design

Jesse runs in a sandboxed environment with explicit permission prompts for sensitive actions. It can't send a message, make a purchase, or delete a file without asking. Draft-only communication means you always review before anything goes out.

This is a deliberate contrast with "god mode" agents that have full system access. If you're running a business, the difference between "drafts for review" and "sends autonomously" is the difference between a useful tool and a liability.

## Iterate, Don't Architect

The instruction file grows organically. Start with a few rules and add more as you hit situations that need them. You won't know what rules you need until you encounter the situations.

Every time the agent does something you'd do differently, refine a rule. The instruction file is a living document. The setup that works well after a few months looks nothing like what you start with.
