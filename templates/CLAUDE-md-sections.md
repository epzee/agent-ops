---
name: claude-md-sections
description: >
  Sections to append to an existing project CLAUDE.md for agent-ops
  integration. Includes plans, thresholds, monitoring, and safety config.
---

# CLAUDE.md sections for agent-ops

Append these sections to your project's existing CLAUDE.md.

---

## Plans

Plans in plans/. Read fully, work in order, verify each task, stop on [blocking].

## Health thresholds

- Test coverage floor: 70%
- Stale PR threshold: 7 days

Mark aspirational thresholds: "Coverage floor: 70% (currently 52%)"

## Monitoring & tools

- Errors: Sentry (MCP: connected / not connected)
- Analytics: PostHog (MCP: connected / not connected)
- CI: GitHub Actions (MCP: connected / not connected)

## Health check overrides

- Skip: [check name] ([reason])

## Maintenance commands

Commands the maintenance agent runs for each check. Set to
"not configured" to skip a check. Add any tool your stack supports.

- Dependency vulnerabilities: (e.g. `npm audit`, `pip-audit`, `govulncheck`)
- Outdated dependencies: (e.g. `npm outdated`, `pip list --outdated`, `go list -m -u all`)
- Unused dependencies: (e.g. `npx depcheck`, `vulture .`) or not configured
- Dead exports: (e.g. `npx ts-prune`, `deadcode ./...`) or not configured
- Security audit: (e.g. `npm audit`, `pip-audit`, `govulncheck`)

## Command safety

All commands safe to run automatically:
- Tests use isolated database
- Build does not deploy
- No interactive input required

Note: the test command is used both by the verification gate and by
the red-green Build loop. If it's "not configured," agent-ops will
propose a Phase 0 bootstrap task on first run.
