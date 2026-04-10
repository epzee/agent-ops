---
name: flaky-tests
description: >
  Detects tests that pass and fail inconsistently across runs. Use
  monthly — needs enough run history to detect patterns.
---

# Flaky test detection

## Setup

Read CLAUDE.md for test command. Detect test runner from config.

## What to check

### Multi-run detection [tool-backed]
- If the test suite takes >2 min per run: skip multi-run detection
  entirely. Analyze CI history and pattern heuristics instead. Note
  in report: "multi-run detection skipped, suite exceeds maintenance
  time budget."
- If any single run produces >500 lines of output: capture only
  failures and summary. Discard passing test output between runs.
- Otherwise: run the test suite 3 times in sequence
- Compare results across runs
- Any test that passes in some runs and fails in others is flaky
- Flag flaky tests as `critical` — they erode trust in the suite

### CI history analysis [heuristic]
- If GitHub MCP is available: check recent CI runs for the same
  test suite on the main branch
- Look for tests that appear in failures intermittently
- Cross-reference with local multi-run results

### Common flakiness patterns [heuristic]
- Grep test files for patterns that cause flakiness:
  - `setTimeout`, `sleep`, `waitFor` with short timeouts
  - Shared mutable state between tests
  - Date/time-dependent assertions without mocking
  - Network calls without mocking (non-integration tests)
  - Port binding (fixed ports in concurrent tests)
- Flag as `info` — these are candidates, not confirmed flaky

## Report format

```
# Flaky Tests — [project] — YYYY-MM-DD

## Critical
- [test file:test name] — failed [n]/[total] runs
  — Error: [brief error message]
  → Action: fix or quarantine — flaky tests mask real failures

## Warning
- [test file:test name] — intermittent in CI (failed [n] of last [m] runs)
  → Action: investigate, likely timing or state issue

## Info
- Total tests: [n], runs: [n]
- Flakiness patterns found in [n] test files
- Common pattern: [most frequent pattern type]
```

