---
name: implement
description: >
  Implement an existing plan file: pre-flight checks, then Build →
  Verify → Review. Use when asked to implement, execute, or resume a
  saved plan (plans/ or docs/plans/).
---

# /agent-ops:implement

Load the agent-ops:pipeline skill and run it in **implement existing
plan** mode on the plan file given as the argument.

Hard constraints (defined in the pipeline skill, repeated because they
gate entry):

- Execute only plans with status **Approved** (fresh start) or
  **In Progress** (resume). Anything else → stop and report.
- Run the pipeline's pre-flight checks before building: read the whole
  plan, honor its Progress Log, verify anchors, check for overlapping
  In Progress plans.
