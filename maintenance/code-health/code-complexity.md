---
name: code-complexity
description: >
  Finds functions with high cyclomatic complexity, deep nesting, and
  excessive length. Use monthly to catch complexity creep before it
  becomes untestable.
---

# Code complexity analysis

## Setup

Read CLAUDE.md and package.json (or equivalent) to detect the stack.
If a complexity tool is configured in `Maintenance commands`, use it.
Otherwise, use heuristic analysis.

## What to check

### Function length [heuristic]
- Grep for function/method definitions
- Flag functions over 50 lines as `warning`, over 100 as `critical`
- Report: file, line number, function name, line count

### Deep nesting [heuristic]
- Scan for indentation depth exceeding 4 levels
- Flag 5+ levels as `warning`, 7+ as `critical`
- Report: file, line number, nesting depth

### Long files [heuristic]
- Check file line counts in source directories
- Flag files over 300 lines as `warning`, over 500 as `critical`
- Exclude generated files, vendor, node_modules, lock files

### Complexity tool [tool-backed]
- If configured: run and parse output
- If not: skip and note "no complexity tool configured"

## Report format

```
# Code Complexity — [project] — YYYY-MM-DD

## Critical
- [file:line] — [function name] — [metric]: [value]
  → Action: [split/extract/refactor recommendation]

## Warning
- [file:line] — [description]
  → Action: [recommendation]

## Info
- Total functions scanned: [n]
- Average function length: [n] lines
- Files exceeding 300 lines: [n]
```

## Comparison

If a previous report exists in health-reports/code-health/, compare
and note trends: "3 new warnings since last month, 1 resolved."
