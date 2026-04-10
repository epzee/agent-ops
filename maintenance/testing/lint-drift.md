---
name: lint-drift
description: >
  Tracks accumulation of inline lint suppressions and disabled rules.
  Use weekly to keep suppressions visible before they get normalized.
---

# Lint rule drift

## Setup

Detect linter from config files: .eslintrc*, .prettierrc*, pylintrc,
.flake8, .golangci.yml, rustfmt.toml, biome.json.

## What to check

### Inline suppressions [heuristic]
- Grep for suppression patterns:
  - JS/TS: `eslint-disable`, `@ts-ignore`, `@ts-expect-error`,
    `@ts-nocheck`, `biome-ignore`
  - Python: `# noqa`, `# type: ignore`, `# pylint: disable`
  - Go: `//nolint`
  - Rust: `#[allow(clippy::*)]`
- Count total and group by rule name
- Flag: any rule suppressed >5 times as `warning`
- Flag: `eslint-disable` without specific rule (blanket) as `critical`

### Disabled rules in config [heuristic]
- Read lint config files
- Count rules set to "off" or 0
- Compare against previous report in health-reports/testing/ if available
- Flag: newly disabled rules since last check as `warning`

### Suppression age [heuristic]
- Run `git blame` on lines with suppressions
- Flag suppressions older than 90 days as `info` — these may be
  permanent workarounds that should become config-level decisions

## Report format

```
# Lint Drift — [project] — YYYY-MM-DD

## Critical
- [file:line] — blanket suppression: [suppression text]
  → Action: add specific rule or remove suppression

## Warning
- [rule name]: suppressed [n] times across [n] files
  → Action: fix violations or disable rule in config with justification
- [rule name]: newly disabled in config since last check
  → Action: justify or re-enable

## Info
- Total suppressions: [n] (new since last: [+/- n])
- Most suppressed rules: [top 5 with counts]
- Config-disabled rules: [n]
```
