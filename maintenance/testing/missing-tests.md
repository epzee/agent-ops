---
name: missing-tests
description: >
  Identifies critical code paths without test coverage. Use monthly —
  the analysis is expensive but catches blind spots.
---

# Missing test identification

## Setup

Read CLAUDE.md and package.json to detect the stack and test
framework. Identify source and test directory conventions.

## What to check

### Source files without test files [heuristic]
- Map source files to expected test file locations using project
  conventions (e.g., `src/foo.ts` → `src/foo.test.ts` or
  `__tests__/foo.test.ts`)
- Flag source files with no corresponding test file
- Prioritize by file type:
  - `critical`: API routes, auth modules, payment/billing, data mutations
  - `warning`: business logic, utilities, hooks
  - `info`: components, config, types

### Coverage gaps in critical paths [tool-backed / heuristic]
- If coverage data is available: find functions with 0% coverage
  in files that handle auth, data, or money
- If not: grep for exported functions and check if they appear
  in any test file

### Recently changed untested code [heuristic]
- `git log --since="30 days"` to find recently modified files
- Cross-reference with test file mapping
- Flag recently changed files without tests as `warning` —
  active code without tests is higher risk

## Report format

```
# Missing Tests — [project] — YYYY-MM-DD

## Critical
- [file] — no tests, handles: [auth/payment/data mutation]
  → Action: add tests for critical path before next change

## Warning
- [file] — no tests, changed [n] times in last 30 days
  → Action: add tests — active code without coverage

## Info
- Source files: [n], with tests: [n], without: [n]
- Test coverage by category:
  API routes: [n]/[total] | Auth: [n]/[total] | Utils: [n]/[total]
```
