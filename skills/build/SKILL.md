---
name: build
description: >
  Build a feature or fix end-to-end: Define → Plan → Build → Verify →
  Review, with two human decisions (approve the plan, approve the
  code). Use when asked to build, add, implement, or fix something
  and no approved plan file exists yet.
---

# /agent-ops:build

Load the agent-ops:pipeline skill and run it in **full pipeline** mode
(steps 1–9) on the request given as the argument.

Bug fixes are behavioral contracts: the reproducer test comes first
(see the test-first skill) — no exceptions.
