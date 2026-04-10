---
name: stale-prs
description: >
  Identifies pull requests and branches that have gone stale. Use
  weekly — PRs older than a week increasingly rot and cause conflicts.
---

# Stale PR cleanup

## Setup

Read CLAUDE.md for `Health thresholds → Stale PR threshold` (default:
7 days). Requires GitHub MCP or `gh` CLI.

## What to check

### Open PRs by age [tool-backed]
- List all open PRs with: title, author, age, last activity date,
  review status, CI status
- Classify:
  - `critical`: older than 3x threshold, no activity in 2+ weeks
  - `warning`: older than threshold
  - `info`: approaching threshold (>50% of threshold age)

### Draft PRs [tool-backed]
- List draft PRs separately
- Flag drafts with no activity in 2+ weeks as `warning`

### Stale branches [tool-backed / heuristic]
- List remote branches not associated with an open PR
- Flag branches with no commits in 30+ days as `info`
- Exclude: main, master, develop, release branches

### Merge conflicts [heuristic]
- For each open PR, check if it has merge conflicts with the
  base branch
- Flag conflicted PRs as `warning` — they need rebase attention

## Report format

```
# Stale PRs — [project] — YYYY-MM-DD

## Critical
- PR #[n]: "[title]" by [author] — [age] days, last activity [date]
  → Action: close or rebase and finish

## Warning
- PR #[n]: "[title]" by [author] — [age] days
  → Action: review or close
- PR #[n]: has merge conflicts with [base]
  → Action: rebase needed

## Info
- Open PRs: [n] (stale: [n], draft: [n], active: [n])
- Stale branches (no open PR, >30 days): [list]
  → Action: delete if work is abandoned
```
