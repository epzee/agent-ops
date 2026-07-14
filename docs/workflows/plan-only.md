# Workflow: Plan Only

Phases: Define → Plan → Review → Save

| Phase | Runs as | Human |
|-------|---------|-------|
| Define (optional) | `refine` skill (forked context) | No |
| Plan | `planner` skill (forked context) | No |
| Review — plan | agent-ops-reviewer agent | **Yes — approve** |
| Save | plan saved to the plans directory | Done |

## Usage

```
/agent-ops:plan [describe what you want]
```

Plan is saved to the project's plans directory (`plans/` or
`docs/plans/`, default `plans/`) as `YYYY-MM-DD-[slug].md` with status
Draft. After **you** approve it, status updates to Approved — only a
human advances a plan to Approved.

## Implementing later

When ready to build:
```
/agent-ops:implement plans/YYYY-MM-DD-[slug].md
```

The pipeline runs pre-flight checks (whole-plan read, progress log,
anchor verification, overlap check) and picks up from the Build phase.
Only plans with status Approved (fresh) or In Progress (resume) are
executed.
