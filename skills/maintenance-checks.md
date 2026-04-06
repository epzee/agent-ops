---
name: maintenance-checks
description: >
  Defines engineering hygiene checks organized by cadence (daily, weekly,
  monthly) plus error triage procedures. Maintenance mode runs tools and
  reads output. Triage mode investigates production errors. Use when
  running health checks or triaging errors.
---

Two sections: **SCHEDULED CHECKS** (maintenance mode, operator) and
**ERROR TRIAGE** (triage mode, investigator).

All tool-backed checks read their commands from the project's CLAUDE.md
under `## Maintenance commands`. If a command is listed as "not configured,"
skip the check and note it in the report.

Labels: [tool-backed] = reliable. [heuristic] = noisy, labeled as such.

---

# Scheduled checks

## Daily (~2 min)

### CI status [tool-backed]
- Run/query: latest CI status on this repo's main branch
- Report: passing or failing, since when, which workflow
- Scoped to current repo only

## Weekly (~5 min)

### Dependency vulnerabilities [tool-backed]
- Run: command from CLAUDE.md → `Maintenance commands` → `Dependency vulnerabilities`
- Report: critical, high, moderate, low counts

### Outdated dependencies [tool-backed]
- Run: command from CLAUDE.md → `Maintenance commands` → `Outdated dependencies`
- Flag: 2+ major versions behind

### Coverage trend [tool-backed]
- Run: coverage command from CLAUDE.md
- Compare: against floor + last week's report
- Flag: below floor, or >2% drop week-over-week

### Configuration integrity [heuristic]
- Check: .env.example vs env var usage in code
- Check: .gitignore coverage
- Note: grep-based, may have false positives

### Stale PRs/branches [tool-backed/heuristic]
- GitHub MCP or git commands
- List with age and author

## Monthly (~15 min)

### Security [tool-backed + heuristic]
- Audit (tool-backed): command from CLAUDE.md → `Maintenance commands` → `Security audit`
- Git history (heuristic): was .env ever committed?
- Grep (heuristic): common secret patterns in source

### Unused dependencies [tool-backed]
- Run: command from CLAUDE.md → `Maintenance commands` → `Unused dependencies`
- Skip if not configured.

### Dead exports [tool-backed]
- Run: command from CLAUDE.md → `Maintenance commands` → `Dead exports`
- Skip if not configured.

### Feature flags [heuristic]
- Grep for flag patterns. Human judges staleness.

### Documentation drift [comparison]
- README commands vs build/run scripts
- CLAUDE.md thresholds vs weekly report reality

## Output format

Report with tables per section:

| Check | Status | Detail |
|-------|--------|--------|
| [name] | ✅/⚠️/❌ | [counts or summary] |

End with **Action items** — numbered list of what needs attention.

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

Save to: health-reports/triage/YYYY-MM-DD.md
