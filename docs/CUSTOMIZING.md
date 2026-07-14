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

## Add an entry-point skill

1. Create `skills/[name]/SKILL.md` with `name` and `description`
   frontmatter. It becomes `/agent-ops:[name]`.
2. Thin wrappers beat restated logic: load the pipeline skill (or
   another contract skill) and state only what differs.
3. Skills that shouldn't appear in the slash menu get
   `user-invocable: false`; skills that should run outside the main
   context get `context: fork`.

## Add a role

Add to `skills/refiner-roles/SKILL.md`. One entry: role name, 1-2
sentences describing what that perspective focuses on.

## Add an agent

Write a skill by default — see the heuristic in
[CONTRIBUTING.md](../CONTRIBUTING.md). If the work genuinely needs an
isolated context (independent judgment, heavy output, background
runs):

1. Create `agents/agent-ops-[name].md`.
2. Include frontmatter: name, description, tools, model, skills.
3. Reference contract skills rather than restating them.

## Model routing

Everything defaults to `model: inherit`. Two overrides worth making:

- **Mechanical work cheaper:** maintenance operator runs (run tool →
  read output → compare threshold) work fine on a fast model — set
  `model: haiku` on agent-ops-maintain if your maintenance runs are
  mostly tool-backed checks. Keep `inherit` if you rely on triage
  mode, which is investigative.
- **Judgment stays strong:** leave the reviewer on `inherit` (or pin
  your strongest model) — verdicts are where model quality shows.

## Evolving checks

Start with what your stack supports. Add checks as you install tools.
Checks that require missing tools are skipped automatically with a note.
