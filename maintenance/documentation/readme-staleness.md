---
name: readme-staleness
description: >
  Checks whether README install steps, examples, and links still work.
  Use monthly — READMEs go stale after feature work, not daily.
---

# README staleness check

## What to check

Read README.md (and any docs/*.md files) in the project root.

### Install steps [heuristic]
- Extract install/setup commands from README
- Verify each command's executable exists (npm, pip, go, cargo, etc.)
- Check referenced files exist (e.g., "copy .env.example to .env"
  — does .env.example exist?)
- Flag broken install steps as `critical`

### Code examples [heuristic]
- Extract code blocks from README
- Check that imported modules/functions exist in the codebase
- Check that file paths in examples are valid
- Flag examples referencing removed code as `warning`

### Links [heuristic]
- Extract all URLs from README and docs
- Check internal links (relative paths) resolve to existing files
- Note external links for manual verification (don't fetch)
- Flag broken internal links as `warning`

### Version references [heuristic]
- Check if README mentions specific versions (Node, Python, etc.)
- Compare against actual project requirements (engines, .nvmrc,
  .python-version, etc.)
- Flag version mismatches as `warning`

### Badges [heuristic]
- If README has CI/coverage badges, verify they point to current
  repo and correct branch
- Flag badges pointing to wrong repo/branch as `info`

## Report format

```
# README Staleness — [project] — YYYY-MM-DD

## Critical
- [file:line] — install step broken: [command/reference]
  → Action: update command or remove outdated step

## Warning
- [file:line] — example references [module/path] which doesn't exist
  → Action: update example
- [file:line] — broken internal link: [path]
  → Action: fix link target

## Info
- Files scanned: [n]
- Install steps: [n] valid, [n] broken
- Links: [n] internal valid, [n] broken, [n] external (unchecked)
```
