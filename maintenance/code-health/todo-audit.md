---
name: todo-audit
description: >
  Audits TODO, FIXME, HACK, and XXX comments across the codebase.
  Use monthly to surface forgotten debt and force keep-or-fix decisions.
---

# TODO/FIXME/HACK comment audit

## What to check

### Scan for markers [heuristic]
- Grep for: `TODO`, `FIXME`, `HACK`, `XXX`, `WORKAROUND`, `TEMP`
- Exclude: node_modules, vendor, .git, lock files, generated files
- For each hit, capture: file, line number, full comment text

### Age analysis
- Run `git blame` on each flagged line
- Calculate age in days from commit date
- Flag: older than 90 days as `warning`, older than 180 as `critical`

### Categorize by type
- `TODO` — planned work, not yet done
- `FIXME` — known broken, needs repair
- `HACK/WORKAROUND` — intentional shortcut, may need cleanup
- `XXX/TEMP` — temporary code that should not ship long-term

## Report format

```
# TODO Audit — [project] — YYYY-MM-DD

## Critical (>180 days old)
- [file:line] — [marker]: "[comment text]"
  — Age: [n] days, author: [name], commit: [sha]
  → Action: fix, remove, or convert to tracked issue

## Warning (>90 days old)
- [file:line] — [marker]: "[comment text]"
  — Age: [n] days
  → Action: review — still relevant?

## Info
- Total markers: [n] (TODO: [n], FIXME: [n], HACK: [n], other: [n])
- New since last audit: [n]
- Resolved since last audit: [n]
```
