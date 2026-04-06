---
name: plan-format
description: >
  Defines the output template for feature plans including task structure,
  verification steps, and scope sections. Use when creating plans for
  features, tests, refactors, or any structured engineering work.
---

# Plan format

Save: plans/YYYY-MM-DD-[slug].md

```markdown
# Feature: [Name]
**Status:** draft | ready | in-progress | shipped | abandoned
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
