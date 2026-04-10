---
name: dependency-freshness
description: >
  Checks for outdated packages and major version gaps. Use weekly to
  catch staleness early when updates are small and safe.
---

# Dependency freshness check

## Setup

Read CLAUDE.md for `Maintenance commands → Outdated dependencies`.
Detect package manager from lock files (package-lock.json, yarn.lock,
pnpm-lock.yaml, Pipfile.lock, go.sum, Cargo.lock, Gemfile.lock).

## What to check

### Outdated packages [tool-backed]
- Run: configured outdated command (npm outdated, pip list --outdated, etc.)
- If not configured: detect package manager and run its native outdated command
- Classify each outdated package:
  - `critical`: 2+ major versions behind
  - `warning`: 1 major version behind
  - `info`: minor/patch updates available

### Pinning hygiene [heuristic]
- Check if dependencies use exact versions, ranges, or latest
- Flag `*` or `latest` as `warning`
- Flag missing lock file as `critical`

### Known deprecations [heuristic]
- Check if any installed packages are marked deprecated
- Flag deprecated packages as `warning`

## Report format

```
# Dependency Freshness — [project] — YYYY-MM-DD

## Critical
- [package]: [current] → [latest] (major: [n] versions behind)
  → Action: review changelog, plan upgrade

## Warning
- [package]: [current] → [latest]
  → Action: update in next maintenance window

## Info
- Total dependencies: [n]
- Up to date: [n]
- Minor/patch updates available: [n]
- Last full update: [date from git history if detectable]
```
