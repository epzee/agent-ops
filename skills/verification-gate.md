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

## Enforcement

**Strict (default):** gate is non-waivable. If asked to skip, explain
why and do not comply unless user explicitly says "override."

**Override:** if user says "override," stamp the artifact:

```
⚠️ UNVERIFIED — gate skipped: [list of checks not run]
```

Stamp persists in subsequent artifacts. Any later reviewer must
account for it in their assessment.

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
