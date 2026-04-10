---
name: maintenance-checks
description: >
  Dispatches scheduled maintenance tasks and handles error triage.
  Maintenance mode reads maintenance/schedules.md and executes the
  appropriate task files for the requested cadence. Triage mode
  investigates production errors. Use when running health checks or
  triaging errors.
---

Two modes: **SCHEDULED CHECKS** (maintenance, operator) and
**ERROR TRIAGE** (triage, investigator).

---

# Scheduled checks

## How dispatch works

0. Read the project's CLAUDE.md once — extract `## Maintenance commands`
   and `## Health thresholds`. Task files reference these sections;
   having them loaded avoids re-reading per task.
1. Read `maintenance/schedules.md` to determine which tasks belong to
   the requested cadence (weekly, monthly, quarterly).
2. For each task in that cadence, read its `.md` file from the
   `maintenance/` subdirectory.
3. Execute the task as written — follow its setup, checks, and report
   format exactly.
4. Each task produces its own detailed report using the standard format:
   `## Critical` / `## Warning` / `## Info` sections with findings,
   file/line references, and recommended actions.
5. After all tasks complete, produce an aggregated summary table.

Task files define report CONTENT format only. The save path is
determined by the agent: health-reports/{category}/{task-name}-YYYY-MM-DD.md

Labels: [tool-backed] = reliable. [heuristic] = noisy, labeled as such.

## Running a single task

When given a specific task file path (e.g., `run maintenance/security/secret-scan.md`),
execute only that task. Skip cadence lookup.

## Aggregated summary format

After all task reports are produced, output one summary table:

```
# Maintenance Summary — [project] — YYYY-MM-DD — [cadence]

| Task | Status | Critical | Warning | Info | Detail |
|------|--------|----------|---------|------|--------|
| [name] | ✅/⚠️/❌ | [n] | [n] | [n] | [top finding] |

### Action items
1. [numbered list of what needs attention, critical first]
```

Individual task reports are saved to `health-reports/{category}/{task-name}-YYYY-MM-DD.md`.
The summary is saved to `health-reports/{cadence}/summary-YYYY-MM-DD.md`.

---

# Error triage

Different philosophy. Investigative, not operator.

Requires error tracking MCP (Sentry, Bugsnag, etc.).

## Process

1. Query errors from error tracking service
2. For each error group:
   - Read stack trace
   - Find source file
   - `git blame` / `git log` on affected lines
   - Check deploy correlation (timing vs recent deploys)
   - Classify:
     - **Real bug:** code error, regression
     - **Expected:** user input, timeout, known edge case
     - **Environment:** third-party service, infrastructure
     - **Noise:** bots, deprecated endpoints, crawlers

3. If real bug: assess severity
   - Crash / data loss / degraded UX / cosmetic

## Spike detection

- Compare error count vs 24-hour average
- If >3x average → identify error group, check for correlated deploy

## Output format

```
# Triage — [project] — YYYY-MM-DD

## Critical (hotfix): [count]
1. [function/endpoint] — [error type]
   — [occurrence count], [user count], since [time/deploy]
   — Likely cause: [analysis]

## Investigate: [count]
1. [description] — [recommendation]

## Silence: [count]
1. [description] — [reason to ignore]
```

Save to: health-reports/triage/triage-YYYY-MM-DD.md
