---
name: planner
description: >
  Plan phase: creates structured plans with runnable verification
  steps, investigated against the real codebase. Used by the pipeline;
  also usable standalone when a task breakdown is needed.
context: fork
user-invocable: false
---

# Planner

Read CLAUDE.md.

Check if the project has a plans directory — plans/ or docs/plans/ —
with a CLAUDE.md or existing plans. If it does, follow the project's
plan conventions (format, metadata, phase structure, naming). Fall back
to the plan-format skill only when no project conventions exist. Use
whichever directory the project actually has; default to plans/ when
neither exists.

## Modes

**Autonomous mode** (called by the pipeline): do NOT ask clarifying
questions unless genuinely [blocking]. Record assumptions as
[assumption] tags. Human reviews at decision point 1.

**Standalone mode** (called by human directly): may ask questions.

## Rules

1. Every task has runnable verification commands.
2. Tasks ≤ 5 files.
3. List files per task.
4. Name risks.
5. Out-of-scope mandatory.
6. Tags: [blocking], [non-blocking], [assumption].
7. Default to test-first ordering. For tasks with behavioral contracts,
   the task's Verify command must be a test that starts RED (asserts the
   new behavior, currently failing). Carve-outs are listed in the
   test-first skill — tag those tasks `[no-tdd: <reason>]`.
8. Check CLAUDE.md for a test command before planning. If missing or
   "not configured," emit a Phase 0 bootstrap task (install runner,
   wire CLAUDE.md test command, add one smoke test) before any feature
   task. See the test-first skill's "Bootstrap" section.
9. Investigate before writing. Plans must name real files, functions,
   and anchors discovered from the actual codebase — read the code,
   don't guess — so execution never requires re-discovery.

Save: [plans dir]/YYYY-MM-DD-[slug].md — plans/ or docs/plans/,
whichever the project has (default plans/). Get the date by running
`date +%F`; never assume the current date.

Summary: tasks, complexity, blockers, assumptions, files.

## Example tasks

```markdown
### 1. Add preference storage schema
**What:** Create preferences table with user_id FK, category enum,
enabled boolean, and quiet_hours JSONB column. Add migration.
**Files:** src/db/migrations/005_preferences.sql, src/db/schema.ts
**Verify:** `bun run migrate && bun test src/db/`
```

```markdown
### 2. Validate preference quiet-hours window
**What:** Add `isWithinQuietHours(prefs, now)` returning true/false
with inclusive start, exclusive end, cross-midnight support.
**Files:** src/prefs/quiet-hours.ts, src/prefs/quiet-hours.test.ts
**Verify:** `bun test src/prefs/quiet-hours.test.ts` (starts RED)
```
