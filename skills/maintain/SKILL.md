---
name: maintain
description: >
  Run scheduled maintenance checks (weekly/monthly/quarterly), a single
  check by path, or production error triage. Spawns the
  agent-ops-maintain agent in its own context. Use when asked for
  health checks, hygiene runs, or error triage.
---

# /agent-ops:maintain

Spawn the agent-ops-maintain agent via the Agent tool, passing the
request verbatim:

- a cadence — "run weekly checks", "run monthly checks", "run
  quarterly checks"
- a single task path — "run maintenance/security/secret-scan.md"
- or triage — "triage errors"

The agent writes reports to health-reports/ and returns a summary.
Relay the summary table and action items to the user.
