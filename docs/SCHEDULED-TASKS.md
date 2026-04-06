# Scheduled tasks

Run maintenance checks automatically on a schedule.

## Options

### Cloud Scheduled Tasks (Claude Code)

Use Claude Code's scheduled task feature to run maintenance agents
on a cron schedule.

Daily:
```
@agent-ops-maintain run daily checks
```

Weekly:
```
@agent-ops-maintain run weekly checks
```

Monthly:
```
@agent-ops-maintain run monthly checks
```

### GitHub Actions

```yaml
name: Weekly maintenance
on:
  schedule:
    - cron: '0 9 * * 1'  # Monday 9am UTC
jobs:
  maintain:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: claude "@agent-ops-maintain run weekly checks"
```

### Cron

```bash
# Weekly on Monday at 9am
0 9 * * 1 cd /path/to/project && claude "@agent-ops-maintain run weekly checks"
```

## Recommended cadence

| Check | Cadence | Time |
|-------|---------|------|
| CI status | Daily | ~2 min |
| Deps, coverage, config, stale | Weekly | ~5 min |
| Security, unused, dead code, docs | Monthly | ~15 min |
| Error triage | Daily or on-demand | Varies |

## MCPs for scheduled tasks

- **Error tracking (Sentry/Bugsnag):** Required for triage mode.
  Without it, triage commands will note the MCP is not connected.
- **GitHub:** Enables CI status and stale PR checks.
- **Analytics (PostHog):** Optional, for usage-related insights.

## Tips

- Start with weekly. Add daily/monthly as you see value.
- Review reports in health-reports/ after each run.
- Adjust thresholds in CLAUDE.md as your project matures.
- Error triage is most valuable run daily or triggered by alerts.
