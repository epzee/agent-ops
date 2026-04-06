---
name: review-criteria
description: >
  Routing logic for review type (plan, code, test), intensity levels
  (quick, thorough, brutal), and the verdict output contract. Use when
  performing independent reviews of any engineering artifact.
---

# Review criteria

## Routing

Determine review type from the artifact:

- **Plan file** → plan review
- **Git diff / code changes** → code review
- **Test files** → test review

## Plan review

Focus areas:
- Clarity: can a builder implement from this alone?
- Verification: every task has runnable commands?
- Scope: out-of-scope section present and reasonable?
- Risks: identified and mitigated?
- Assumptions: tagged and reasonable?

## Code review

**Prerequisite:** gate must pass first. Gate failure = notice, not verdict.

Focus areas:
- Correctness: does it do what the plan says?
- Security: input validation, auth, injection vectors
- Coverage: new code has tests?
- Complexity: simpler approach possible?

## Test review

Focus areas:
- Behavior over mocks: testing real behavior, not implementation details?
- Assertion quality: meaningful assertions, not just "doesn't throw"?
- Edge cases: boundaries, empty states, error paths?
- Independence: tests don't depend on execution order?

## Intensity levels

| Level | Trigger | Scope |
|-------|---------|-------|
| Quick | "quick review" | Critical issues only |
| Thorough | default | Full review + discovered skills |
| Brutal | "be brutal", "brutal review" | All + style, naming, design questioning |

## Output contract

```
Verdict: [exactly one, unmodified:
  Ready to implement | Ship it | Needs revision | Needs significant rework]

Blocking findings: [count]
1. [location] — [problem] — [suggested fix]

Non-blocking suggestions: [count]
1. [location] — [suggestion]
```

## Rules

- Verdict is one of four options. No qualifiers on the verdict line.
- Gate failure is a separate notice, NOT a verdict.
- Detail goes in findings, not the verdict.
- Note any stamps (UNVERIFIED / REVIEWER OVERRIDDEN / UNREVIEWED) in findings.
