# Changelog

## 2.0.0 — 2026-07-14

Restructure for current Claude Code plugin capabilities. Breaking:
entry points changed from `@agent-ops` mentions to `/agent-ops:*`
commands.

### Changed

- **Skills are real plugin skills** — migrated flat `skills/*.md`
  files to `skills/<name>/SKILL.md` so Claude Code discovers them and
  agents' `skills:` preloads resolve.
- **Skills by default, agents for isolation** — the coordinator,
  refiner, and planner agents became skills. The pipeline runs in the
  main conversation (human decision points require it); refine and
  planner run in forked contexts. Only the reviewer (context
  isolation) and maintainer (heavy background runs) remain agents.
- **Entry points are slash commands** — `/agent-ops:build`, `:plan`,
  `:implement`, `:refine`, `:review`, `:maintain`,
  `:verification-gate`, `:test-first`.
- **workflows/ moved to docs/workflows/** — they are reference docs,
  not loadable plugin assets.
- Single source of truth for contracts: verdict format and stamp
  protocol live in one skill each; agents reference instead of
  restating.
- SCHEDULED-TASKS: routines first, `anthropics/claude-code-action`
  for GitHub, `claude -p` for cron (previous example was not valid
  headless syntax).

### Added

- **Hooks** — Stop hook blocks ending a pipeline turn on a failing
  gate; SubagentStop hook holds the reviewer to the verdict contract.
- **Plan-execution safety** — execute only Approved (fresh) or
  In Progress (resume) plans; only humans advance plans to Approved.
  Pre-flight checks: whole-plan read, Progress Log as source of truth,
  anchor verification, overlapping-plan detection.
- **Project plan-convention deference** — plans/CLAUDE.md or
  docs/plans/CLAUDE.md governs lifecycle, branching, progress logging,
  and completion handling; both plans/ and docs/plans/ supported.
- Gate scope discipline: a gate that can't pass within the task's
  scope records a blocker and escalates instead of widening scope.
- Dates come from `date +%F`, never assumed.
- Planner rule: investigate before writing — plans name real files and
  functions from the codebase.
- Repo CI: JSON, frontmatter, and internal-link validation.
- Recommended `.claude/settings.json` permission allowlist in SETUP.

## 1.0.0 — 2026-04

Initial release: coordinator + 4 specialist agents, 6 skills,
4 workflows, 26 maintenance tasks, plugin manifests.
