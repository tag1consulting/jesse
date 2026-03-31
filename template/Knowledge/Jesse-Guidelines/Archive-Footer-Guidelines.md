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
- Checking both "Archive" and "Deep extract" is treated as "Deep extract." Checking all three is invalid -- nothing happens.
- When none are checked, the file stays active in its current directory.

The footer uses a searchable pattern: `grep -rl '^\- \[x\] \(Archive\|Deep extract\|Archive only\)' Projects/drafts/ Projects/Research/` finds all checked files. Note: QMD lex cannot match `[x]` -- its tokenizer strips brackets. Always use grep for checkbox detection.
