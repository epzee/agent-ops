# Setup

## Claude Code (full experience)

```bash
claude plugin marketplace add https://github.com/epzee/agent-ops
claude plugin install agent-ops
```

## Other tools

Use the markdown body of any skill or agent file as a system prompt.
The autonomous pipeline requires subagent support. Independent
reviewer context requires isolated subagent sessions. Hook
enforcement requires Claude Code.

If your project uses AGENTS.md instead of CLAUDE.md, import it:
create a CLAUDE.md containing `@AGENTS.md` (Claude Code reads the
import), or run `/init` to generate a CLAUDE.md from it.

## Project configuration

1. **Add CLAUDE.md sections.** Copy sections from
   `templates/CLAUDE-md-sections.md` into your project's CLAUDE.md.

2. **Set thresholds.** Update coverage floor, stale PR threshold, and
   other values to match your project.

3. **Configure commands.** Ensure your CLAUDE.md has test, typecheck,
   lint, and build commands that the verification gate can run. The
   test command is also the prerequisite for the red-green Build loop
   (test-first skill). If your project has no test harness yet,
   agent-ops will run a Phase 0 bootstrap task on first use.

4. **Grant permissions declaratively.** The gate runs your test,
   typecheck, lint, and build commands repeatedly — allowlist them in
   `.claude/settings.json` instead of approving each run, and deny
   anything with side effects:

   ```json
   {
     "permissions": {
       "allow": [
         "Bash(npm test:*)",
         "Bash(npm run typecheck:*)",
         "Bash(npm run lint:*)",
         "Bash(npm run build:*)"
       ],
       "deny": [
         "Bash(npm publish:*)",
         "Bash(git push:*)"
       ]
     }
   }
   ```

   Substitute your stack's commands. This is enforcement the harness
   applies — stronger than the honor-system "Command safety" note in
   CLAUDE.md, which you should still fill in for human readers.

5. **Verify setup.** Run `/agent-ops:refine what does this project do`
   to confirm the plugin is loaded and can read your project, and
   `/hooks` to confirm the two agent-ops hooks are registered.

## Optional

### Scheduled tasks

See [SCHEDULED-TASKS.md](SCHEDULED-TASKS.md) for weekly/monthly/quarterly
automation via routines, GitHub Actions, or cron.

### MCPs

- **Error tracking:** Connect Sentry or Bugsnag MCP for triage mode.
- **Analytics:** Connect PostHog MCP for usage insights.
- **CI:** Connect GitHub Actions MCP for CI status checks.

### Skill packs

Install [agent-skills](https://github.com/addyosmani/agent-skills) for
deeper methodology at each phase. The pipeline discovers and uses
these automatically.
