# Workflow: Feature

Phases: Define → Plan → Build → Verify → Review → Ship

| Phase | Runs as | Human |
|-------|---------|-------|
| Define (optional) | `refine` skill (forked context) | No |
| Plan | `planner` skill (forked context) | No |
| Review — plan | agent-ops-reviewer agent | **Yes — approve** |
| Build | main conversation, red-green per task | No |
| Simplify | 3 parallel review agents | No |
| Verify (enforced) | gate | No. Fails → fix (max 3) |
| Review — code | agent-ops-reviewer agent (gate first) | **Yes — ship or fix** |
| Ship | | **Yes** |

## Automated

```
/agent-ops:build [describe feature]
```

The pipeline runs end-to-end. You make two decisions:
1. Approve the plan (after plan review)
2. Approve the code (after code review)

## Manual

1. `/agent-ops:refine [idea]` → refined prompt
2. Ask for a plan from the refined prompt (planner skill / plan-format)
3. `/agent-ops:review [plan file]` → plan verdict
4. Build tasks from plan (red-green per the test-first skill)
5. Run simplify pass: spawn reuse, quality, efficiency reviews on `git diff`, fix findings
6. `/agent-ops:verification-gate`
7. `/agent-ops:review the working-tree diff` → code verdict
8. Ship
