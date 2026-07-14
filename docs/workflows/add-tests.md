# Workflow: Add Tests

Phases: Define → Plan → Build → Verify → Review

| Phase | Runs as | Human |
|-------|---------|-------|
| Define (optional) | `refine` skill (forked context) | No |
| Plan | `planner` skill (test plan) | No |
| Review — plan | agent-ops-reviewer agent | **Yes** |
| Build | implement tests | No |
| Verify (enforced) | new + existing tests + types + lint | No |
| Review — quality | agent-ops-reviewer ("be brutal") | **Yes** |

## Usage

```
/agent-ops:build add test coverage for [area]
```

## Notes

- Planner produces a test plan, not a feature plan
- Verification runs both new and existing tests (no regressions)
- Reviewer uses brutal intensity for test quality
- Focus: behavior over mocks, assertion quality, edge cases
