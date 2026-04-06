---
name: add-tests
description: >
  Test coverage workflow: Define, Plan, Build, Verify, Review.
  For adding test coverage to existing code.
---

# Workflow: Add Tests

Phases: Define → Plan → Build → Verify → Review

| Phase | Agent | Human |
|-------|-------|-------|
| Define (optional) | @agent-ops-refiner | No |
| Plan | @agent-ops-planner (test plan) | No |
| Review — plan | @agent-ops-reviewer | **Yes** |
| Build | implement tests | No |
| Verify (enforced) | new + existing tests + types + lint | No |
| Review — quality | @agent-ops-reviewer ("be brutal") | **Yes** |

## Notes

- Planner produces a test plan, not a feature plan
- Verification runs both new and existing tests (no regressions)
- Reviewer uses brutal intensity for test quality
- Focus: behavior over mocks, assertion quality, edge cases
