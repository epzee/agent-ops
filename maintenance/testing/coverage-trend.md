---
name: coverage-trend
description: >
  Tracks test coverage direction over time, not just current percentage.
  Use weekly to catch gradual erosion before it becomes a project.
---

# Test coverage trend analysis

## Setup

Read CLAUDE.md for coverage command and `Health thresholds → Test
coverage floor`. Detect test runner from package.json or config files.

## What to check

### Current coverage [tool-backed]
- Run: coverage command from CLAUDE.md, or detect and run:
  jest --coverage, vitest --coverage, pytest --cov, go test -cover
- Parse: line/branch/function/statement percentages
- If no coverage tool: skip with note

### Trend comparison
- Read previous report from health-reports/testing/ if it exists
- Compare: current vs last week
- Flag: drop >2% in any metric as `warning`
- Flag: below floor threshold as `critical`

### Uncovered areas [heuristic]
- From coverage output, list the 10 files with lowest coverage
- Focus on source files (not test files, configs, or generated code)

## Report format

```
# Coverage Trend — [project] — YYYY-MM-DD

## Critical
- Coverage below floor: [metric] at [current]% (floor: [threshold]%)
  → Action: add tests to bring above threshold

## Warning
- [metric] dropped [n]%: [previous]% → [current]%
  → Action: review recent changes for untested code

## Info
- Line: [n]% | Branch: [n]% | Function: [n]%
- Trend: [↑/↓/→] [n]% since last report
- Lowest coverage files:
  1. [file] — [n]%
  2. [file] — [n]%
  (up to 10)
```
