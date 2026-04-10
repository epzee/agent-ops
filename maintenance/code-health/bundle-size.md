---
name: bundle-size
description: >
  Tracks build output and bundle size over time. Use monthly to catch
  gradual bloat that no single PR introduces.
---

# Bundle size / build output tracking

## Setup

Read CLAUDE.md and package.json to detect the build system.
Look for: next, vite, webpack, esbuild, rollup, tsc, expo.

## What to check

### Build output size [tool-backed]
- Run the project's build command
- Run the build in a temporary directory or with output redirected
  if the build system supports it. If the build modifies the working
  directory, note this in the report.
- Measure total output size and individual chunk/file sizes
- Compare against previous report if one exists in health-reports/code-health/

### Largest files [heuristic]
- List the 10 largest files in the build output
- Flag any single file over 500KB as `warning`, over 1MB as `critical`

### Source map presence [heuristic]
- Check if source maps are included in production build output
- Flag source maps in production as `warning` (security + size)

### Tree-shaking effectiveness [heuristic]
- If the bundler supports it, check for tree-shaking warnings
- Note any packages that can't be tree-shaken

## Report format

```
# Bundle Size — [project] — YYYY-MM-DD

## Critical
- [file/chunk]: [size] — exceeds 1MB threshold
  → Action: code-split or lazy-load this chunk

## Warning
- [file/chunk]: [size] — exceeds 500KB threshold
  → Action: investigate what's contributing to size
- Source maps found in production output
  → Action: exclude from production build

## Info
- Total build output: [size]
- Change since last report: [+/- size] ([+/- %])
- Largest chunks: [top 5 with sizes]
- Build time: [duration]
```

If no build system is detected, report `info: no build system found,
skipping bundle size check` and exit cleanly.
