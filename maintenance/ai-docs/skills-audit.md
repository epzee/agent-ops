---
name: skills-audit
description: >
  Verifies skills in .claude/skills/ still match the codebase and
  don't reference removed patterns. Use monthly — skills change less
  often than CLAUDE.md.
---

# Skills audit

## What to check

Scan all skill files in `.claude/skills/` and any installed plugin
skill directories.

### File/path references [heuristic]
- Extract file paths mentioned in each skill
- Verify they exist in the codebase
- Flag missing as `warning`

### Tool references [heuristic]
- Extract tool/command references from skill content
- Verify tools are installed and commands work
- Flag broken tool references as `warning`

### Pattern references [heuristic]
- Extract code patterns, function names, or conventions described
  in skills
- Grep the codebase to verify these patterns still exist
- Flag patterns not found as `warning` — the skill is teaching
  the agent about code that no longer exists

### Frontmatter validity [heuristic]
- Check each skill has valid YAML frontmatter with `name` and
  `description`
- Verify name follows convention (kebab-case, ≤64 chars)
- Verify description follows convention (third person, what + when)
- Flag invalid frontmatter as `warning`

### Redundancy check [heuristic]
- Compare skill descriptions for overlap
- Flag skills with >80% similar descriptions as `info`

## Report format

```
# Skills Audit — [project] — YYYY-MM-DD

## Critical
- [skill file] — references [path] which no longer exists, skill may
  produce incorrect guidance
  → Action: update skill to match current codebase

## Warning
- [skill file:line] — references [symbol/pattern] not found
  → Action: update or remove reference
- [skill file] — invalid frontmatter: [issue]
  → Action: fix frontmatter

## Info
- Skills scanned: [n]
- Valid: [n], Issues found: [n]
- Potentially redundant: [list pairs]
```
