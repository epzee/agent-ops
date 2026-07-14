# Scheduled tasks

Run maintenance checks automatically on a schedule.

## Options (in order of recommendation)

### 1. Claude Code routines (recommended)

Create a scheduled cloud agent with `/schedule` (or the desktop app):

```
/schedule weekly "/agent-ops:maintain run weekly checks"
/schedule monthly "/agent-ops:maintain run monthly checks"
```

Routines run on Anthropic infrastructure in their own session against
your repo — no CI wiring or cron host needed.

### 2. GitHub Actions

Use the official action; headless prompts go through it rather than a
raw `claude` call:

```yaml
name: Weekly maintenance
on:
  schedule:
    - cron: '0 9 * * 1'  # Monday 9am UTC
jobs:
  maintain:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "/agent-ops:maintain run weekly checks"
```

Reports land in `health-reports/` in the workspace; add a step to
commit them or upload as an artifact if you want them persisted.

### 3. Cron

Headless mode requires the `-p` flag:

```bash
# Weekly on Monday at 9am
0 9 * * 1 cd /path/to/project && claude -p "/agent-ops:maintain run weekly checks"
```

### In-session: /loop

For continuous feedback during long refactors, run a single check on
an interval inside an active session:

```
/loop 30m /agent-ops:maintain run maintenance/testing/coverage-trend.md
```

## Recommended cadence

| Cadence | Tasks | Estimated time |
|---------|-------|----------------|
| Weekly | deps, vulns, secrets, coverage, lint, stale PRs, CLAUDE.md | ~15 min |
| Monthly | complexity, dead code, TODOs, bundle, licenses, flaky, missing tests, errors, perf, deploys, skills, prompt drift, README, API docs, ecosystem, AI docs | ~30 min |
| Quarterly | OWASP, best practices, changelog | ~45 min |
| On-demand | Error triage | varies |

## MCPs for scheduled tasks

- **Error tracking (Sentry/Bugsnag):** Required for triage mode.
  Without it, triage commands will note the MCP is not connected.
- **GitHub:** Enables CI status and stale PR checks.
- **Analytics (PostHog):** Optional, for usage-related insights.

Note: interactively-authenticated MCP servers may be unavailable in
headless or scheduled runs — tasks that need them skip and note it.

## Tips

- Start with weekly. Add monthly as you see value.
- Review reports in health-reports/ after each run.
- Adjust thresholds in CLAUDE.md as your project matures.
- Error triage is most valuable run daily or triggered by alerts.
