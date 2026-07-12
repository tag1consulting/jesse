# Archive Footer Guidelines

Every file in `Projects/drafts/` and `Projects/Research/` (not `archive/` subdirectories) must end with an archive footer -- checkboxes that the user marks to indicate what should happen when they're done reviewing:

```markdown
---

- [ ] Archive (extract key info, then archive)
- [ ] Deep extract (extract with a lower bar for what qualifies, then archive)
- [ ] Archive only (skip extraction, archive as-is)
```

**Rules:**

- "Archive" extracts what clearly matters -- decisions, action items, key findings, named people, dates, commitments -- then moves to the appropriate `archive/` subdirectory. Use judgment: skip boilerplate, routine details, and information already captured elsewhere in the vault.
- "Deep extract" lowers the bar for what qualifies as worth extracting. Background context, supporting details, minor observations, and tangential information that "Archive" would skip all get captured. Still skip genuine noise (errors, unrelated timestamps, redundant information already in the vault), but when in doubt, extract it.
- "Archive only" moves to archive without extraction. For files that were simple or whose content doesn't propagate.
- Checking both "Archive" and "Deep extract" is treated as "Deep extract." Checking all three is invalid -- report this to the user rather than processing silently, so they can correct their intent.
- When none are checked, the file stays active in its current directory.

**Detection:**

The footer uses a searchable pattern. Always use grep for checkbox detection -- QMD's tokenizer strips brackets, so `[x]` never matches in lex queries.

```sh
grep -rEl '^- \[x\] (Archive|Deep extract|Archive only)' Projects/drafts/ Projects/Research/
```

The footer standardizes on hyphen-style list markers (`-`). Obsidian also renders `*` as list items, but the grep pattern expects hyphens -- always use hyphens in the footer.

Note: the paths above (`Projects/drafts/`, `Projects/Research/`) may need adjusting per vault layout.
