---
name: top-errors
description: >
  Triages top errors from the project's error tracking service. Use
  monthly to catch chronic issues that daily noise obscures.
---

# Top errors from error tracking

**Requires:** error tracking MCP (Sentry, Bugsnag, etc.) — produces
no results without it.

## Setup

Read CLAUDE.md for `Monitoring & tools → Errors` to determine the
error tracking service and MCP connection status.

If no error tracking MCP is connected, skip with note:
"Error tracking not configured. Add Sentry/Bugsnag MCP to enable."

## What to check

### Top error groups [tool-backed]
- Query: top 10 error groups by frequency in the last 30 days
- For each error group:
  - Error type and message
  - Occurrence count and affected user count
  - First seen / last seen dates
  - Stack trace summary (file and function)

### Classification
For each error group, classify as:
- **Real bug**: code error, regression, logic flaw
- **Expected**: user input validation, timeout, known edge case
- **Environment**: third-party service, infrastructure, network
- **Noise**: bots, deprecated endpoints, crawlers

### Severity assessment
- `critical`: crashes, data loss, affects >5% of users
- `warning`: degraded UX, affects <5% of users, non-data-loss
- `info`: cosmetic, expected errors, noise

## Report format

```
# Top Errors — [project] — YYYY-MM-DD

## Critical
- [error type] in [file:function] — [n] occurrences, [n] users
  — Classification: [real bug/expected/environment/noise]
  → Action: [specific fix or investigation step]

## Warning
- [error type] — [n] occurrences
  → Action: [recommendation]

## Info
- Total error groups: [n]
- New since last report: [n]
- Resolved since last report: [n]
- Classification breakdown: bugs [n], expected [n], env [n], noise [n]
```
