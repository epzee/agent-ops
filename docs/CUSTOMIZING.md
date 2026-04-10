# Customizing

## Check types

**Tool-backed:** Real tool with structured output. Reliable. Preferred.

**Heuristic:** Grep/find/shell-based. Noisy signal. Always labeled
`[heuristic]` in output so humans know to expect false positives.

## Add a check

1. Create a markdown file in `maintenance/{category}/`.
2. Add YAML frontmatter with `name` and `description`.
3. Label checks `[tool-backed]` or `[heuristic]`.
4. Define: what to run, what to report, what threshold flags it.
5. Add the task to `maintenance/schedules.md` with cadence and rationale.

## Remove a check

Comment out the task line in `maintenance/schedules.md`, or add it to
your project's CLAUDE.md under `## Health check overrides`:

```markdown
- Skip: [check name] ([reason])
```

## Stack adaptation

Maintenance checks read commands from your project's CLAUDE.md under
`## Maintenance commands`. Fill in the tools your stack supports:

| Check | JS/TS | Python | Go |
|-------|-------|--------|----|
| Dependency vulnerabilities | `npm audit` | `pip-audit` | `govulncheck` |
| Outdated dependencies | `npm outdated` | `pip list --outdated` | `go list -m -u all` |
| Unused dependencies | `npx depcheck` | `vulture .` | (manual) |
| Dead exports | `npx ts-prune` | `vulture .` | `deadcode ./...` |
| Security audit | `npm audit` | `pip-audit` | `govulncheck` |

Set any check to "not configured" to skip it. The check structure
(run tool → read output → compare threshold) is universal.

## Add a workflow

1. Copy `workflows/_template.md`.
2. Define phases, agents, gates, and human decision points.
3. Follow the pattern: each phase names the agent, gate requirement,
   and whether human approval is needed.

## Add a role

Add to `skills/refiner-roles.md`. One entry: role name, 1-2 sentences
describing what that perspective focuses on.

## Add an agent

1. Create `agents/agent-ops-[name].md`.
2. Include frontmatter: name, description, tools, model, skills.
3. Include skill discovery section in the system prompt.
4. Add `agent-ops-` prefix to the name.

## Evolving checks

Start with what your stack supports. Add checks as you install tools.
Checks that require missing tools are skipped automatically with a note.
