---
name: plan
description: >
  Plan now, build later: Define → Plan → Review, saving an approved
  plan for later implementation. Use when asked to plan, spec, or
  scope work without building it yet.
---

# /agent-ops:plan

Load the agent-ops:pipeline skill and run it in **plan only** mode
(steps 1–4) on the request given as the argument.

The plan is saved to the project's plans directory (plans/ or
docs/plans/, default plans/) with status Draft. After the user
approves it, update status to Approved — only a human approval
advances a plan to Approved.

Implement later with `/agent-ops:implement [plan path]`.
