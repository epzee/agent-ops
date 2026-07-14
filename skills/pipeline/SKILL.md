---
name: pipeline
description: >
  The agent-ops Define → Plan → Build → Verify → Review pipeline with
  enforced gates, red-green builds, and two human decision points.
  Loaded by the build, plan, and implement entry points; not run
  directly.
user-invocable: false
---

# agent-ops pipeline

Runs in the main conversation — the human decision points require it.
Read the project's CLAUDE.md before starting.

Beyond the agent-ops skills, installed project and plugin skills
surface automatically — load the ones relevant to the stack and task
before the phase that needs them. Don't load all.

## Roles and contexts

- **Refine** — the agent-ops:refine skill (forked context).
- **Plan** — the agent-ops:planner skill (forked context).
- **Review** — the agent-ops-reviewer agent, ALWAYS spawned via the
  Agent tool with only the artifact. Never review inline and never
  @-mention the reviewer — sharing this conversation's context defeats
  the isolation that makes the review independent.
- **Build** — you, in this conversation, per the test-first skill.

## Modes

- **Full pipeline** (`/agent-ops:build`) → steps 1–9.
- **Plan only** (`/agent-ops:plan`) → steps 1–4. Save plan. STOP.
- **Implement existing plan** (`/agent-ops:implement`) → pre-flight
  checks, then steps 5–9. Execute only status **Approved** (fresh
  start) or **In Progress** (resume). Any other status — Draft,
  Blocked, Ready, Complete, Shipped, Abandoned, or missing — STOP and
  report to the user, regardless of how the request is phrased. Only
  a human advances a plan to Approved.

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

## Circuit breaker

3 consecutive failures → STOP. Escalate with:
- Current artifact (plan or diff)
- Last findings (what keeps failing)
- What each attempt tried
- Unresolved questions

## Enforcement

Strict mode (default): gate and reviewer are non-waivable. Override
requires the explicit word "override." The override and stamp protocol
(UNVERIFIED / REVIEWER OVERRIDDEN / UNREVIEWED) is defined in the
verification-gate skill — stamps persist in subsequent artifacts, and
in strict mode without override: explain why and do not comply.

## Pipeline

1. Run the agent-ops:refine skill on the user's input → refined prompt.
2. Run the agent-ops:planner skill with the refined prompt → plan file.
3. Spawn agent-ops-reviewer (Agent tool) with the plan file path.
   - "Needs revision" → route blocking findings to the planner skill
     for revision. Re-spawn the reviewer on the revised plan. Max 3 loops.
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
   - Otherwise, run the red-green inner loop from the test-first skill:
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
   Otherwise: spawn agent-ops-reviewer (Agent tool) with instructions
   to review the git diff. Reviewer runs its own gate independently
   first.
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

3. **You MUST spawn agent-ops-reviewer via the Agent tool (step 8).**
   Separate context, only the artifact as input. Do not review your
   own code inline, and do not @-mention the reviewer — either way it
   would see the builder's reasoning and anchor to it.

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
