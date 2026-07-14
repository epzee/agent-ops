---
name: plan-format
description: >
  Defines the output template for feature plans including task structure,
  verification steps, and scope sections. Use when creating plans for
  features, tests, refactors, or any structured engineering work.
---

# Plan format

Save: [plans dir]/YYYY-MM-DD-[slug].md — plans/ or docs/plans/,
whichever the project has (default plans/). Get the date by running
`date +%F`; never assume the current date.

If the project's plans directory has its own CLAUDE.md, its format and
status vocabulary override this template.

```markdown
# Feature: [Name]
**Status:** Draft | Approved | In Progress | Blocked | Complete | Abandoned
**Author:** [name] | **Created:** [date] | **Target:** [version]

## Problem
2-3 sentences.

## Solution
Approach, not implementation.

## Tasks
### 1. [name]
**What:** One paragraph.
**Files:** Expected files.
**Verify:** [runnable commands — not "make sure it works"]

## Out of scope
Mandatory. What's related but NOT in this plan.

## Open questions
[blocking] Must resolve before implementation.
[non-blocking] Can resolve during or after.
[assumption] Recorded in autonomous mode — human reviews at decision point.

## Risks
Data migration? API contract? Performance?
```

## Rules

- Verify = runnable commands only.
- Tasks ≤ 5 files.
- Out-of-scope mandatory.
- Only a human moves a plan to Approved. Agents create plans as Draft
  and may set In Progress / Blocked / Complete / Abandoned during
  execution per the coordinator's rules.
