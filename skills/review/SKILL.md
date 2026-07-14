---
name: review
description: >
  Independent review of a plan, code diff, or tests. Spawns the
  isolated agent-ops-reviewer agent so the verdict is not anchored by
  this conversation. Use when asked for a review or second opinion.
---

# /agent-ops:review

Spawn the agent-ops-reviewer agent via the Agent tool. Pass ONLY:

- the artifact reference — a plan file path, "review the working-tree
  diff", a PR number, or test file paths
- the requested intensity if the user gave one (quick / thorough /
  brutal; e.g., "be brutal")

Do NOT include this conversation's implementation reasoning, design
discussion, or justifications — isolation from the builder's context
is the point of the independent review. Do not review inline in this
conversation.

Relay the reviewer's verdict and findings verbatim, then add your own
take only if the user asks for it.
