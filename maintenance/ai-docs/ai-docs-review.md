---
name: ai-docs-review
description: >
  Reviews all AI documentation for prompt quality, cross-file consistency,
  reference integrity, and executability. Use monthly — agent prompts
  drift from codebase reality; catches issues before agents fail silently.
---

# AI docs review

## Scope detection

If `agents/` exists at repo root with `.md` files containing agent
frontmatter, treat the entire repo as AI docs. Otherwise, scan
`.claude/agents/`, `.claude/skills/`, `.claude/commands/`, and any
installed plugin directories.

## What to check

CLAUDE.md refs covered by `claude-md-freshness.md`. Skills refs covered
by `skills-audit.md`. Do not repeat those — this task covers everything else.

### 1. Prompt engineering quality [heuristic]

Scan all agent, skill, command, and workflow files.

- **Vague instructions:** flag "appropriately", "as needed", "relevant",
  "properly", "ensure", "consider" — words that give no actionable
  guidance. Report line and suggest a concrete rewrite.
- **Constraint ordering:** critical constraints (tool restrictions, write
  permissions, output limits) must appear in the first third of the file.
  Flag any that appear after the halfway point.
- **Internal contradictions:** conflicting instructions within a single
  file (e.g., "read only" + write instruction, "never" + later "always"
  for the same action).

### 2. Cross-file consistency [heuristic]

- **Terminology:** severity levels, cadence names, report headers, tool
  names must be consistent across files. Flag deviations (e.g., "Error"
  vs "Critical", "biweekly" vs "fortnightly").
- **Frontmatter vs body:** verify agent frontmatter (tools, model) matches
  body instructions. Flag mismatches (e.g., body uses Write but tools
  doesn't list it).
- **Concept duplication:** multiple files defining the same concept
  differently (different report formats, different output paths).

### 3. Reference integrity [heuristic]

For agent, command, and workflow files (not CLAUDE.md or skills):

- **File paths:** verify referenced paths exist. Flag missing as `critical`.
- **Tool names:** verify tools in instructions match frontmatter. Flag
  undeclared as `warning`.
- **Cross-references:** verify file-to-file refs point to real files.
- **Consumer-project refs:** refs to CLAUDE.md sections that can't be
  verified here — note as `info: verify in consumer project`.

### 4. Agent executability [heuristic]

For each agent/skill file, walk through the instructions:

- **Undeclared dependencies:** instructions assuming tools or MCPs not
  declared in frontmatter or setup sections.
- **Context window risk:** unbounded output — no limits on file reads,
  grep results, or repeated tool calls that could blow context.
- **Dead-end paths:** instructions where an agent could get stuck with
  no fallback (e.g., "run X" with no degradation path).
- **Circular references:** files referencing each other in ways that
  could cause execution loops.

### 5. AI doc README accuracy [heuristic]

For READMEs in AI doc directories (general READMEs covered by
`readme-staleness.md`):

- **File counts/lists:** stated counts match actual directory contents.
- **Example invocations:** commands would work as written.
- **Hardcoded values:** version numbers or counts that will go stale.

## Report format

```
# AI Docs Review — [project] — YYYY-MM-DD

## Critical
- [file:line] — [category]: [finding]
  → Action: [concrete fix]

## Warning
- [file:line] — [category]: [finding]
  → Action: [concrete fix]

## Info
- Files scanned: [n] (agents: [n], skills: [n], commands: [n], other: [n])
- Prompt quality: [n] issues, Consistency: [n], References: [n], Executability: [n]
- Deferred to: claude-md-freshness, skills-audit
```
