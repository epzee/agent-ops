---
name: verification-gate
description: >
  Defines the four enforced quality checks (tests, typecheck, lint, build)
  and enforcement rules. Use when running verification gates between
  pipeline phases or during maintenance checks.
---

# Verification gate

Four checks, all sourced from CLAUDE.md commands:

1. **Tests** — run the project's test command
2. **Typecheck** — run the project's type-checking command
3. **Lint** — run the project's linter (errors only, not warnings)
4. **Build** — run the project's build command

All four must pass. Run sequentially. Stop on first failure for faster feedback.

## Retry

Max 3 attempts (circuit breaker). On failure:
1. Read the error output
2. Fix the issue
3. Re-run the failing check

After 3 consecutive failures → escalate to user with:
- Which check failed
- Error output from each attempt
- What was tried

## Scope discipline

If the gate cannot pass within the current task/phase's stated scope,
do not commit the failing state and do not expand scope to force it
green. Record the blocker, set the plan's status per the project
lifecycle (e.g., Blocked), and escalate.

## Enforcement and override stamps

**Strict (default):** the gate and the reviewer are non-waivable. If
asked to skip either, explain why and do not comply unless the user
explicitly says "override."

**Override:** if the user says "override," stamp the artifact:

- Gate skipped → `⚠️ UNVERIFIED — gate skipped: [checks not run]`
- Reviewer findings overridden → `⚠️ REVIEWER OVERRIDDEN — findings
  ignored: [list]`
- Review skipped entirely → `⚠️ UNREVIEWED — no review performed`

Stamps persist in subsequent artifacts. Any later reviewer must
account for them in its assessment. This section is the single source
of truth for the override protocol — agents and skills reference it
rather than restating it.

## Gate failure vs reviewer verdict

Gate failure is a **pre-check notice**, NOT a reviewer verdict.
The four verdict options (Ready to implement / Ship it / Needs revision /
Needs significant rework) only apply after a review has been conducted.

A gate failure means: "fix this before review can begin."

## Safety

Project owner ensures commands in CLAUDE.md are safe:
- No production database access
- No side effects (deploys, notifications)
- No interactive input required
- Tests use isolated environments
