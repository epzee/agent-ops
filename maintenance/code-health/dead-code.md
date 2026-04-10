---
name: dead-code
description: >
  Detects unused exports, unreachable code, and orphaned files. Use
  monthly to prevent dead code from accumulating into confusion.
---

# Dead code / unused exports detection

## Setup

Read CLAUDE.md for `Maintenance commands → Dead exports`. Read
package.json to determine the stack and available tooling.

## What to check

### Dead exports [tool-backed]
- Run: configured dead export tool (ts-prune, deadcode, etc.)
- If not configured: skip with note
- Flag: any unused export as `warning`
- Flag: unused exports in public API surface as `critical`

### Orphaned files [heuristic]
- Find source files not imported/required by any other file
- Exclude: entry points, config files, test files, scripts
- Cross-reference with build config entry points
- Flag as `info` — human confirms before removal

### Unreachable code [heuristic]
- Grep for common patterns: `return` followed by code, `if (false)`,
  commented-out function bodies, `// dead`, `// unused`
- Flag as `info` — high false-positive rate, label accordingly

## Report format

```
# Dead Code — [project] — YYYY-MM-DD

## Critical
- [file:line] — unused public export: [name]
  → Action: remove or mark as internal

## Warning
- [file:line] — unused export: [name]
  → Action: verify unused, then remove

## Info
- [file] — possible orphan (not imported anywhere)
  → Action: confirm not an entry point, then remove
- Unreachable patterns found: [count]
```

Label all heuristic findings as `[heuristic — verify before acting]`.
