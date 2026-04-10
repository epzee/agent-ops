---
name: performance-regression
description: >
  Detects gradual performance degradation in bundle size, build time,
  and API latency. Use monthly — needs trend data to spot regressions.
---

# Performance regression detection

## Setup

Read CLAUDE.md for monitoring tools and health thresholds.
Read package.json to detect the stack and build system.

## What to check

### Build performance [tool-backed]
- Run the build and measure wall-clock time
- Compare against previous report if one exists in health-reports/production/
- Flag: >20% increase in build time as `warning`
- Flag: >50% increase as `critical`

### Bundle size
Handled separately by `code-health/bundle-size.md`. Do not duplicate —
skip bundle size analysis in this task.

### Test suite speed [tool-backed]
- Run the test suite and measure total execution time
- Compare against previous report in health-reports/production/
- Flag: >20% increase as `warning`
- Identify slowest 5 test files

### API latency [tool-backed]
- If monitoring MCP is connected (PostHog, Grafana):
  query p50/p95/p99 latency for key endpoints
- Compare against previous month
- Flag: p95 >500ms as `warning`, p95 >1s as `critical`
- If no monitoring: skip with note

## Report format

```
# Performance — [project] — YYYY-MM-DD

## Critical
- [metric]: [current] (was [previous]) — [% change]
  → Action: [investigate specific cause]

## Warning
- [metric]: [current] (was [previous]) — [% change]
  → Action: [recommendation]

## Info
- Build time: [duration] ([trend])
- Test time: [duration] ([trend])
- Bundle size: see code-health/bundle-size.md
- API p95: [duration] ([trend]) — or "not configured"
```
