# Maintenance schedules

Maps every task to a cadence. @agent-ops-maintain reads this file to
determine which tasks to run for `weekly`, `monthly`, or `quarterly`.

Comment out lines to disable tasks you don't need yet.

---

## Why no daily cadence

Daily agent runs are expensive (tokens, time). CI failures are better
caught by CI notification systems (GitHub, Slack). All agent-driven
checks run weekly at minimum.

## Weekly (~15 min total)

| Task | File | Why this cadence |
|------|------|-----------------|
| Dependency freshness | `code-health/dependency-freshness.md` | Catching outdated deps weekly prevents the quarterly "update everything" panic. Small version bumps are safe; stale ones compound. |
| Vulnerability scan | `security/vulnerability-scan.md` | New CVEs drop daily. Weekly is the minimum responsible cadence — monthly means 30 days of known exposure. |
| Secret scan | `security/secret-scan.md` | One leaked key can compromise everything. Weekly catches accidental commits before they propagate to forks or caches. |
| Coverage trend | `testing/coverage-trend.md` | Coverage erodes gradually — no single PR drops it much, but weekly trending catches the drift before it becomes a project. |
| Lint drift | `testing/lint-drift.md` | Inline suppressions accumulate fast. Weekly keeps them visible so they get fixed instead of normalized. |
| Stale PRs | `production/stale-prs.md` | PRs older than a week are increasingly likely to rot. Weekly cleanup prevents merge conflicts and context loss. |
| CLAUDE.md freshness | `ai-docs/claude-md-freshness.md` | AI context files go stale fast as code changes. Stale CLAUDE.md = bad agent output across every session. |

## Monthly (~30 min total)

| Task | File | Why this cadence |
|------|------|-----------------|
| Code complexity | `code-health/code-complexity.md` | Complexity grows slowly. Monthly catches the trend without generating noise on normal feature work. |
| Dead code | `code-health/dead-code.md` | Dead code accumulates over weeks, not days. Monthly is enough to catch unused exports before they become archaeological layers. |
| TODO audit | `code-health/todo-audit.md` | TODOs left for a month are either real debt or forgotten promises. Monthly surfaces them for a keep-or-fix decision. |
| Bundle size | `code-health/bundle-size.md` | Bundle size creeps with new deps and features. Monthly tracking catches the trend; per-PR checks catch the spikes. |
| License compliance | `security/license-compliance.md` | Licenses change with dependency updates, not daily. Monthly catches new incompatibilities after the deps settle. |
| Flaky tests | `testing/flaky-tests.md` | Flaky tests need enough run history to detect patterns. Monthly gives a meaningful sample size. |
| Missing tests | `testing/missing-tests.md` | New untested code lands weekly, but the "what's missing" analysis is expensive. Monthly balances cost vs coverage. |
| Top errors | `production/top-errors.md` | Error patterns need time to emerge. Monthly triage catches chronic issues that daily noise obscures. |
| Performance regression | `production/performance-regression.md` | Perf regressions need trend data. Monthly comparison catches gradual degradation that no single deploy reveals. |
| Deployment frequency | `production/deployment-frequency.md` | Deploy cadence is a trailing indicator. Monthly review shows whether process changes improved throughput. |
| Skills audit | `ai-docs/skills-audit.md` | Skills change less often than CLAUDE.md. Monthly catches drift without redundant weekly checks. |
| Agent prompt drift | `ai-docs/agent-prompt-drift.md` | Agent instructions should track codebase conventions. Monthly alignment check prevents slow divergence. |
| README staleness | `documentation/readme-staleness.md` | READMEs go stale after feature work, not daily. Monthly catches outdated install steps and dead links. |
| API docs coverage | `documentation/api-docs-coverage.md` | New endpoints ship without docs regularly. Monthly catches the gaps before they become tribal knowledge. |
| Ecosystem audit | `ai-docs/ecosystem-audit.md` | Tooling changes with dependency updates and new installations. Monthly catches unmet task dependencies and surfaces missing skills/MCPs before they cause silent failures. |

## Quarterly (~45 min total)

| Task | File | Why this cadence |
|------|------|-----------------|
| OWASP surface | `security/owasp-surface.md` | Deep security surface analysis is expensive and findings are structural, not transient. Quarterly matches security review cadence. |
| Best practices audit | `ai-docs/best-practices-audit.md` | Framework best practices evolve slowly. Quarterly catches major version shifts and new recommendations. |
| Changelog gaps | `documentation/changelog-gaps.md` | Changelog gaps only matter at release boundaries. Quarterly aligns with most teams' release documentation cycle. |
