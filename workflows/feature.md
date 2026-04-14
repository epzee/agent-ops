---
name: feature
description: >
  Full feature workflow: Define, Plan, Build, Verify, Review, Ship.
  Use @agent-ops for automation, or follow steps manually.
---

# Workflow: Feature

Phases: Define → Plan → Build → Verify → Review → Ship

| Phase | Agent | Human |
|-------|-------|-------|
| Define (optional) | @agent-ops-refiner | No |
| Plan | @agent-ops-planner | No |
| Review — plan | @agent-ops-reviewer | **Yes — approve** |
| Build | @agent-ops or manual (red-green per task) | No |
| Simplify | 3 parallel review agents | No |
| Verify (enforced) | gate | No. Fails → fix (max 3) |
| Review — code | @agent-ops-reviewer (gate first) | **Yes — ship or fix** |
| Ship | | **Yes** |

## Automated

```
@agent-ops [describe feature]
```

The coordinator runs the full pipeline. You make two decisions:
1. Approve the plan (after plan review)
2. Approve the code (after code review)

## Manual

1. `@agent-ops-refiner [idea]` → refined prompt
2. `@agent-ops-planner [refined prompt]` → plan file
3. `@agent-ops-reviewer [plan file]` → plan verdict
4. Build tasks from plan
5. Run simplify pass: spawn reuse, quality, efficiency reviews on `git diff`, fix findings
6. Run verification gate
7. `@agent-ops-reviewer [git diff]` → code verdict
8. Ship
