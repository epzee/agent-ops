---
name: claude-md-freshness
description: >
  Verifies CLAUDE.md references to files, functions, commands, and
  patterns still exist in the codebase. Use weekly — stale AI context
  produces bad agent output across every session.
---

# CLAUDE.md freshness check

## What to check

Read all CLAUDE.md files in the project (root, .claude/, subdirectories).

### File path references [heuristic]
- Extract every file path mentioned in CLAUDE.md
- Check each path exists in the filesystem
- Flag missing files as `critical` — agents will hallucinate around
  references to files that don't exist

### Function/symbol references [heuristic]
- Extract function names, class names, variable names mentioned
- Grep the codebase for each symbol
- Flag symbols not found as `warning`

### Command references [heuristic]
- Extract shell commands (lines starting with backtick blocks,
  indented code, or inline code with command-like patterns)
- Verify executables exist (e.g., if it references `npx depcheck`,
  check depcheck is installed)
- Flag broken commands as `critical`

### Architecture descriptions [heuristic]
- Extract directory structure descriptions or component lists
- Compare against actual directory structure
- Flag significant discrepancies as `warning`

### Threshold accuracy [heuristic]
- Extract numeric thresholds (coverage floor, PR age, etc.)
- If measurable: compare against current reality
- Flag aspirational thresholds not marked as such as `info`

## Report format

```
# CLAUDE.md Freshness — [project] — YYYY-MM-DD

## Critical
- [CLAUDE.md file:line] — references [path/command] which no longer exists
  → Action: update or remove reference

## Warning
- [CLAUDE.md file:line] — references [symbol] not found in codebase
  → Action: verify renamed/removed, update reference
- [CLAUDE.md file:line] — architecture description doesn't match actual structure
  → Action: update directory listing

## Info
- CLAUDE.md files scanned: [n]
- Total references checked: [n]
- Valid: [n], Stale: [n]
```
