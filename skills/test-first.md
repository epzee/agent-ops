---
name: test-first
description: >
  Red-green discipline for the Build phase: failing test first, confirm it
  fails for the right reason, then minimum code to green. Use when
  implementing tasks with behavioral contracts.
---

# Test-first (red-green)

Default ON for any task with a behavioral contract. Skip only for the
carve-outs below.

## The loop (per task)

1. **Red.** Write the failing test(s) from the task's acceptance criteria.
2. **Confirm red is honest.** Run the test. It must fail on an assertion
   mismatch — not import error, syntax error, missing fixture, or compile
   failure. If it errors wrong, fix the test before writing any
   implementation code.
3. **Green.** Write the minimum implementation to pass the test.
4. **Confirm green.** Run the test. If still red after 3 fix attempts,
   stop and escalate (circuit breaker).
5. **Task verify.** Run the task's declared verification commands from
   the plan.

No implementation code may exist before step 2 has confirmed a
right-reason failure.

## Carve-outs — skip the loop

Tag the task `[no-tdd: <reason>]` in the plan. Valid reasons:

- **UI polish** — spacing, colors, copy tweaks with no behavioral change.
- **Config** — environment variables, feature-flag scaffolding, tool
  settings.
- **Schema migrations** — run forward + rollback instead.
- **Docs** — README, comments, plan files.
- **Pure refactor** — existing tests already pin behavior; run them
  before and after to prove no regression.

Anything not on this list defaults to red-green.

## Bug fixes

A bug fix is a behavioral contract. The failing test IS the reproducer.
Write it first, watch it fail, then fix. This is non-negotiable — a
bug fix without a reproducer test is a regression waiting to happen.

## Bootstrap — no test harness yet

Check CLAUDE.md for a test command before planning or building. If
missing or set to "not configured":

1. **Planner** emits a Phase 0 bootstrap task: install the project's
   test runner, wire it into CLAUDE.md's test command, and add one
   smoke test that asserts `1 + 1 === 2` (or equivalent). The Verify
   command for this task is the test runner itself — it proves the
   harness exists and runs.
2. **Coordinator** runs Phase 0 before any feature task. Once Phase 0
   is green, resume normal red-green on remaining tasks.
3. `[no-tdd: no-test-harness]` is NOT a valid carve-out. If a user
   asks to skip bootstrap, explain why and refuse unless they say
   "override" — same convention as the verification gate.

Bootstrap once, then red-green forever.

## Deeper methodology

If the `agent-skills:test-driven-development` plugin skill is available
(discovered at runtime per the agent's "Skill discovery" section), load
it for non-trivial tasks — it carries the full red-green rubric and the
Prove-It bug pattern. This bundled skill is the floor; the plugin is
the depth.
