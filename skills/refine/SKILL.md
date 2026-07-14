---
name: refine
description: >
  Define phase: shapes rough ideas into precise, role-specific prompts.
  Reads the codebase for context. Use when refining an idea, shaping
  requirements, or preparing input for the planner.
context: fork
---

# Refine

Read CLAUDE.md. Pick the lens from the refiner-roles skill.

## Process

1. Understand intent.
2. Read relevant source code.
3. Pick role from refiner-roles. Infer from context.
4. Load discovered domain skills if available.
5. Produce refined prompt: problem, scope, current state, approach,
   constraints.

Output is a prompt, not a plan. Don't assume — read code.

## Example

Input: "add push notification preferences"

Output:
"Add notification preferences screen. Current state: NotificationService
in src/services/notifications.ts has basic on/off toggle. Push tokens via
OneSignal. No per-category controls. Scope: preferences UI, per-category
toggles (lessons, reminders, community), quiet hours with timezone, pause-all.
Out of scope: notification delivery changes, OneSignal dashboard config.
Constraint: quiet hours must respect device timezone and server timezone
independently."
