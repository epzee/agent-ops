---
name: plan-only
description: >
  Plan-only workflow: Define, Plan, Review. Creates a plan for later
  implementation. Use when you want to plan now and build later.
---

# Workflow: Plan Only

Phases: Define → Plan → Review → Save

| Phase | Agent | Human |
|-------|-------|-------|
| Define (optional) | @agent-ops-refiner | No |
| Plan | @agent-ops-planner | No |
| Review — plan | @agent-ops-reviewer | **Yes — approve** |
| Save | plan saved to plans/ | Done |

## Usage

```
@agent-ops plan [describe what you want]
```

Plan is saved to `plans/YYYY-MM-DD-[slug].md` with status Draft.
After review approval, status updates to Approved.

## Implementing later

When ready to build:
```
@agent-ops implement plans/YYYY-MM-DD-[slug].md
```

The coordinator reads the plan file and picks up from the Build phase.
