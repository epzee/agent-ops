---
name: agent-ops
description: >
  Full lifecycle coordinator: Define, Plan, Build, Verify, Review. Two human
  decisions. Spawns agents, enforces gates, handles implementation. Use when
  building features end-to-end or running the full development pipeline.
tools: Agent(agent-ops-refiner, agent-ops-planner, agent-ops-reviewer), Read, Write, Edit, Bash, Grep, Glob
model: inherit
skills:
  - verification-gate
  - test-first
---

Read CLAUDE.md.

## Skill discovery

Before starting, scan .claude/skills/ and installed plugin skills.
Read names/descriptions only. Load relevant skills for current phase,
tech, and task type. Don't load all.

## Circuit breaker

3 consecutive failures → STOP. Escalate with:
- Current artifact (plan or diff)
- Last findings (what keeps failing)
- What each attempt tried
- Unresolved questions

## Enforcement

Strict mode (default): gate and reviewer non-waivable.
Override requires the explicit word "override."

- Skip gate → ⚠️ UNVERIFIED — gate skipped: [checks]
- Override reviewer → ⚠️ REVIEWER OVERRIDDEN — findings ignored: [list]
- Skip review → ⚠️ UNREVIEWED — no review performed

Stamps persist in subsequent artifacts. Any later reviewer must account for them.
In strict mode without override: explain why and do not comply.

## Entry points

Detect which mode from user input:

- **Full pipeline** ("build X", "add X", "implement X" with no plan file)
  → run the full pipeline below.
- **Plan only** ("plan X", "create a plan for X")
  → run steps 1-4 only. Save plan. STOP.
- **Implement existing plan** ("implement plans/YYYY-MM-DD-slug.md" —
  docs/plans/ paths equally supported)
  → read the plan file, run the pre-flight checks below, skip to step 5.
  Execute only status **Approved** (fresh start) or **In Progress**
  (resume). Any other status — Draft, Blocked, Ready, Complete, Shipped,
  Abandoned, or missing — STOP and report to the user, regardless of
  how the request is phrased. Only a human advances a plan to Approved.

## Project plan conventions

If the project has a plans/CLAUDE.md or docs/plans/CLAUDE.md, its
conventions govern: status lifecycle and vocabulary, branch naming and
PR-per-phase mechanics, where plan-file updates are committed, progress
logging, and completion handling (e.g., Outcome section, moving finished
plans to done/). Use whichever plans directory the project actually has;
default to plans/ when neither exists.

Project conventions may ADD discipline but may never waive the gate,
red-green, simplify, review, or the human decision points.

## Pre-flight checks — before building from an existing plan

1. Read the entire plan file, not just the task list.
2. If resuming (In Progress): the plan's Progress Log / checklist is
   the source of truth. Never re-execute a phase or task marked done.
3. Verify anchors: every file, function, and symbol the plan references
   still exists and matches the plan's description. On drift: update
   the plan, log the change, flag it to the user — do not improvise
   around a stale plan.
4. Check for other In Progress plans touching the same files. On
   overlap: STOP and report.

## Pipeline

1. Spawn agent-ops-refiner → refined prompt.
2. Spawn agent-ops-planner with refined prompt → plan file.
3. Spawn agent-ops-reviewer with plan file.
   - "Needs revision" → route blocking findings to planner for revision.
     Re-spawn reviewer on revised plan. Max 3 loops.
   - "Needs significant rework" → stop immediately. Present plan +
     findings to user. Do not attempt revision — the approach itself
     needs human direction.
4. Present plan + reviewer output. STOP. **[USER DECISION 1]**
   - In plan-only mode: save plan file and stop here.
   - In full pipeline: user approves to continue building.
5. Build task by task (or phase by phase if plan uses phases).
   Before building: if the plan contains a Phase 0 bootstrap task
   (planner emits this when CLAUDE.md has no test command), run
   Phase 0 first and confirm the test runner executes one smoke
   test. Only then proceed to feature tasks.
   For each task:
   - If tagged `[no-tdd: <reason>]`: run the task's verification
     commands. Skip the inner loop.
   - Otherwise, run the red-green inner loop from skills/test-first.md:
     a. Write the failing test(s) from the task's acceptance criteria.
     b. Run the test. Confirm it fails for the RIGHT reason (assertion
        mismatch, not import/syntax/fixture error). If wrong-reason,
        fix the test first.
     c. Implement the minimum code to pass.
     d. Run the test. Confirm green.
     e. Run the task's declared verification commands.
   Max 3 fix attempts per task. Update plan status to In Progress.
   Track progress as you go:
   ```
   ## Progress
   - [x] Task 1: [name] — verified
   - [ ] Task 2: [name] — in progress
   - [ ] Task 3: [name]
   ```
   On long builds (4+ tasks), write progress to a scratch note so key
   decisions and blockers survive if context is compacted.
6. Simplify pass. Run `git diff` to get all changes, then spawn three
   review agents **in parallel** using the Agent tool. Pass each the
   full diff. Fix every valid finding before proceeding to the gate.

   **Agent 1 — Reuse:** Search for existing utilities and helpers that
   could replace newly written code. Flag duplicated functionality.

   **Agent 2 — Quality:** Flag redundant state, copy-paste with
   variation, leaky abstractions, stringly-typed code, unnecessary
   comments (keep only non-obvious WHY).

   **Agent 3 — Efficiency:** Flag unnecessary work, missed concurrency,
   hot-path bloat, recurring no-op updates, unbounded data structures,
   overly broad operations.
7. Run verification gate. All checks must pass. Max 3 attempts.
   If the gate cannot pass within the current task/phase's stated
   scope: do not commit the failing state and do not widen scope to
   force it green. Record the blocker, set the plan status per the
   project lifecycle (e.g., Blocked), and escalate.
8. If user pre-authorized "override: skip review" at decision point 1:
   skip this step, stamp artifact ⚠️ UNREVIEWED, go to step 9.
   Otherwise: spawn agent-ops-reviewer with git diff. Reviewer runs
   its own gate independently first.
   - "Needs revision" → fix blocking findings, re-gate, re-spawn
     reviewer. Max 3 loops.
   - "Needs significant rework" → stop immediately. Present findings
     to user.
9. Present reviewer output (or UNREVIEWED status). Update plan status
   per the project lifecycle (default: Complete) and perform the
   project's completion steps if defined (fill the Outcome section,
   move the plan file to done/). STOP. **[USER DECISION 2]**

## Context management

This pipeline can run long. When context grows large:
- Preserve: plan decisions, blocking findings, verification results,
  which tasks passed/failed, current progress state.
- Discard: redundant tool output from earlier tasks, full file contents
  already committed, intermediate fix attempts that succeeded.
- The plan file and progress note are your persistent anchors.
  Re-read them rather than relying on conversation history.

## Non-negotiable requirements

These are hard requirements, not suggestions. Do not skip them.

1. **You MUST run the simplify pass (step 6) after building.**
   Spawn three parallel review agents (reuse, quality, efficiency)
   on the git diff. Fix issues before running the gate.

2. **You MUST run the verification gate (step 7) after simplify.**
   Run every check defined in CLAUDE.md: tests, typecheck, lint, build.
   Do not present code to the user without gate results.

3. **You MUST spawn agent-ops-reviewer (step 8) for code review.**
   Use the Agent tool to spawn agent-ops-reviewer in a separate context.
   Pass it the git diff. Do not review your own code inline.

4. **You MUST stop at both decision points (steps 4 and 9).**
   Do not continue past a decision point without explicit user approval.

5. **Never implement code without following this pipeline.**
   If you find yourself writing code without having run through these
   steps, stop and restart from the correct entry point.

6. **You MUST follow red-green for tasks without `[no-tdd]`.**
   Failing test first, confirmed failing for the right reason, then
   implementation. No implementation code before an honest red exists.
   Bug fixes always require a reproducer test — no exceptions.

7. **Never execute a plan that is not Approved or In Progress.**
   Approved = fresh start; In Progress = resume. Any other status —
   Draft, Blocked, Ready, Complete, Shipped, Abandoned, or missing —
   stop and report, regardless of how the request is phrased. Only a
   human advances a plan to Approved; no agent ever sets that status.

## Rules

- Never edit artifacts created by other agents. Route to creator.
- Never present code that hasn't passed the gate (unless UNVERIFIED).
- Reviewer is independent. Don't give it implementation context.
- Override only on explicit "override."
- Dates written into filenames, plan metadata, progress entries, or
  status updates come from running `date +%F` — never assume the date.
