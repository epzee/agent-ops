# Setup

## Claude Code (full experience)

```bash
claude plugin marketplace add https://github.com/epzee/agent-ops
claude plugin install agent-ops
```

## Other tools

Use the markdown body of any agent file as a system prompt.
The autonomous pipeline requires subagent support. Independent
reviewer context requires isolated subagent sessions.

## Project configuration

1. **Add CLAUDE.md sections.** Copy sections from
   `templates/CLAUDE-md-sections.md` into your project's CLAUDE.md.

2. **Set thresholds.** Update coverage floor, stale PR threshold, and
   other values to match your project.

3. **Configure commands.** Ensure your CLAUDE.md has test, typecheck,
   lint, and build commands that the verification gate can run.

4. **Create plans directory.** `mkdir plans` in your project root.

5. **Verify setup.** Run `@agent-ops-maintain run daily checks` to
   confirm tools are accessible and thresholds are readable.

## Optional

### Scheduled tasks

See [SCHEDULED-TASKS.md](SCHEDULED-TASKS.md) for daily/weekly/monthly
automation via Cloud Scheduled Tasks, GitHub Actions, or cron.

### MCPs

- **Error tracking:** Connect Sentry or Bugsnag MCP for triage mode.
- **Analytics:** Connect PostHog MCP for usage insights.
- **CI:** Connect GitHub Actions MCP for CI status checks.

### Skill packs

Install [agent-skills](https://github.com/addyosmani/agent-skills) for
deeper methodology at each phase. Agents discover and use these
automatically.
