---
name: deployment-frequency
description: >
  Tracks deployment frequency and lead time as DORA-adjacent metrics.
  Use monthly — deploy cadence is a trailing indicator that needs a
  month of data to show meaningful trends.
---

# Deployment frequency and lead time tracking

## Setup

Read CLAUDE.md for CI/deployment configuration. Detect deployment
method from: GitHub Actions workflows, Vercel/Netlify config,
Dockerfile, deploy scripts.

## What to check

### Deployment frequency [tool-backed / heuristic]
- If GitHub MCP available: count deployments/releases in last 30 days
- Otherwise: count tags, releases, or deploy-related commits
  (`git log --grep="deploy\|release" --since="30 days"`)
- Compare against previous month
- Classify: daily (elite), weekly (high), monthly (medium),
  less (low) — per DORA benchmarks

### Lead time [heuristic]
- For the last 10 merged PRs: measure time from first commit
  to merge
- Calculate: median and p90 lead time
- Flag: median >5 days as `warning`, >14 days as `critical`

### Change failure rate [heuristic]
- If CI data available: count failed deployments vs total
- If revert commits detectable: count reverts in last 30 days
- Flag: >15% failure rate as `warning`, >30% as `critical`

### PR merge rate [tool-backed]
- Count PRs opened vs merged vs closed in last 30 days
- Flag: growing open PR count as `warning` (throughput issue)

## Report format

```
# Deployment Metrics — [project] — YYYY-MM-DD

## Critical
- [metric]: [value] — below healthy threshold
  → Action: [specific process improvement]

## Warning
- [metric]: [value] — trending in wrong direction
  → Action: [recommendation]

## Info
- Deployments (30d): [n] ([trend]) — classification: [DORA level]
- Lead time (median): [duration] | p90: [duration]
- Change failure rate: [n]%
- PRs opened: [n] | merged: [n] | closed: [n]
- Comparison vs last month: [summary]
```
