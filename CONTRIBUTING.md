# Contributing

## What belongs

- **Checks:** tool-backed preferred, heuristic labeled. Structure:
  run tool → read output → compare threshold.
- **Workflows:** phase sequence with agents, gates, human decisions.
- **Roles:** for the refiner. 1-2 sentences per role.
- **Setup guides:** installation and configuration.
- **Review criteria:** routing, intensity, verdict contract.

## Guidelines

- Tool → output → threshold. Every check follows this pattern.
- Label heuristics. If it uses grep/find, say `[heuristic]`.
- Separate maintenance (operator) from triage (investigator).
- No skill pack dependencies. agent-ops works standalone.
- Guard MCPs. If a check needs an MCP, note it. Skip gracefully if absent.
- Separate verdict from findings. Verdict is one of four options,
  period. Detail goes in the findings list.
- Gate failure is a notice, not a verdict.

## What doesn't belong

- **Methodology:** that's what skill packs (like agent-skills) provide.
- **Project config:** belongs in the project's CLAUDE.md, not here.
- **Reports:** generated output, not source material.
- **Credentials:** never commit secrets or API keys.

## File conventions

- Agent files: `agents/agent-ops-[name].md`
- Skill files: `skills/[name].md`
- Workflow files: `workflows/[name].md`
- All markdown except LICENSE and .gitignore.
- YAML frontmatter on agent and skill files:
  - `name`: max 64 chars, lowercase/numbers/hyphens only
  - `description`: max 1024 chars, third person, what + when

## Pull request checklist

- [ ] Follows file conventions
- [ ] Checks labeled [tool-backed] or [heuristic]
- [ ] No skill pack dependencies
- [ ] MCPs guarded (skip if absent)
- [ ] Verdict/findings separation maintained
- [ ] Under 500 lines per skill file
