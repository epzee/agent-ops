---
name: changelog-gaps
description: >
  Finds shipped commits and releases without corresponding changelog
  entries. Use quarterly — aligned with release documentation cycles.
---

# Changelog gaps

## Setup

Detect changelog file: CHANGELOG.md, CHANGES.md, HISTORY.md, or
releases section in README. If no changelog exists, report as `info`
and suggest creating one.

## What to check

### Commits without changelog entries [heuristic]
- Read the changelog to find the last documented version/date
- Run `git log` for commits after that date
- Filter to meaningful commits (exclude: merge commits, version
  bumps, dependency updates, CI config, typo fixes)
- Flag user-facing changes without changelog entries as `warning`
- Flag breaking changes without entries as `critical`

### Release tags without entries [heuristic]
- List git tags that look like versions (v*, semver pattern)
- Check each tag has a corresponding changelog section
- Flag missing release entries as `warning`

### Changelog format consistency [heuristic]
- Check if changelog follows a consistent format (Keep a Changelog,
  conventional commits, custom)
- Flag format inconsistencies as `info`
- Check date formatting consistency

### Breaking changes [heuristic]
- Grep commits for breaking change indicators:
  `BREAKING`, `breaking:`, `!:` in commit messages
- Verify each has a changelog entry with migration guidance
- Flag undocumented breaking changes as `critical`

## Report format

```
# Changelog Gaps — [project] — YYYY-MM-DD

## Critical
- Breaking change without changelog entry:
  [commit sha] — [message]
  → Action: add changelog entry with migration guidance

## Warning
- [n] user-facing commits since last changelog entry ([date])
  — Notable: [list top 5 commit messages]
  → Action: update changelog before next release

## Info
- Last changelog entry: [version/date]
- Commits since: [n] (meaningful: [n], filtered: [n])
- Release tags without entries: [n]
- Changelog format: [detected format]
```

If no changelog file exists:
```
## Info
- No changelog file found
  → Action: consider creating CHANGELOG.md using Keep a Changelog format
```
