# Contributing to Jesse

Thanks for wanting to contribute. The most useful thing you can do is share a recipe.

## What's a Recipe?

A recipe is a self-contained guide for setting up a specific Jesse capability. Examples: "Birthday reminders with Obsidian Reminders," "Scanning WhatsApp for action items," "Weekly network health checks via UniFi."

## Adding a Recipe

1. Fork the repo and create a branch.
2. Add a markdown file in `docs/src/recipes/`. Use the template below.
3. Add your recipe to the table of contents in `docs/src/SUMMARY.md`.
4. Open a pull request with a short description of what the recipe does.

## Recipe Template

```markdown
# Recipe Title

One paragraph: what this recipe does and why you'd want it.

## Prerequisites

What you need before starting (tools, accounts, MCP connectors, plugins).

## JESSE.md Configuration

The instruction file sections to add or modify. Include the actual markdown
to paste into the instruction file.

## Vault Structure

Any new files or directories needed in the vault.

## How It Works

Step-by-step explanation of what happens during a session when this recipe
is active.

## Tips and Gotchas

Things that tripped you up or took iteration to get right.
```

## Other Contributions

Found a bug in the docs? Typo in the template? Improvement to the getting started guide? PRs welcome for those too.

## Building the Site Locally

The site uses [mdbook](https://rust-lang.github.io/mdBook/). To preview changes:

```bash
# Install mdbook (requires Rust)
cargo install mdbook

# Build and serve
cd docs/
mdbook serve --open
```

## Code of Conduct

Be helpful, be kind, be specific. This is a project about making people's lives easier.
