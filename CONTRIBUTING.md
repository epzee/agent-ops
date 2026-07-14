# Contributing

## Skill or agent?

**Write a skill by default.** A skill is instructions plus an
invocation surface (`/agent-ops:name`, auto-discovery); an agent is a
separate execution context. Promote to an agent only when the work
needs one of:

- **Context isolation as a feature** — the independent reviewer must
  not see the builder's reasoning.
- **Context protection or parallelism** — heavy tool output that would
  pollute the main conversation, or N concurrent workers.
- **Background execution** — scheduled or long-running work with no
  human interaction.

**Never encode a human approval inside an agent.** Subagents cannot
pause to ask the user anything — approvals live in main-thread skills
(that's why the pipeline is a skill and the reviewer is an agent).

## What belongs

- **Checks:** tool-backed preferred, heuristic labeled. Structure:
  run tool → read output → compare threshold.
- **Entry-point skills:** phase sequence with gates and human decisions.
- **Roles:** for the refine skill. 1-2 sentences per role.
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
- One source of truth. Contracts (verdict format, stamp protocol, plan
  format) live in exactly one skill; agents and other skills reference
  them instead of restating them.

## What doesn't belong

- **Methodology:** that's what skill packs (like agent-skills) provide.
- **Project config:** belongs in the project's CLAUDE.md, not here.
- **Reports:** generated output, not source material.
- **Credentials:** never commit secrets or API keys.

## File conventions

- Skill files: `skills/[name]/SKILL.md`
- Agent files: `agents/agent-ops-[name].md` (isolation cases only)
- Maintenance tasks: `maintenance/[category]/[name].md`
- All markdown except LICENSE, .gitignore, and JSON manifests.
- YAML frontmatter on agent and skill files:
  - `name`: max 64 chars, lowercase/numbers/hyphens only
  - `description`: max 1024 chars, third person, what + when
  - skills not meant for the slash menu: `user-invocable: false`
  - skills that should run outside the main context: `context: fork`

## Pull request checklist

- [ ] Follows file conventions
- [ ] Skill-by-default: new agents justify their isolation need
- [ ] Checks labeled [tool-backed] or [heuristic]
- [ ] No skill pack dependencies
- [ ] MCPs guarded (skip if absent)
- [ ] Verdict/findings separation maintained
- [ ] Contracts stated once, referenced elsewhere
- [ ] Under 500 lines per skill file
