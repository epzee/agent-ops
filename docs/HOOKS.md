# Hooks

agent-ops ships two hooks (`hooks/hooks.json`) that make enforcement
deterministic instead of prompt-only. Prompt instructions can drift on
long runs; hooks are evaluated by the harness on every matching event.

## Stop — gate enforcement

Fires when the main conversation tries to end a turn. If an agent-ops
pipeline is active and the build phase completed without a passing
verification gate (or an explicit override stamp), the hook blocks the
stop and names the unmet requirement. Stops at the two human decision
points are always allowed — those are the pipeline working as designed.

Outside a pipeline run the hook approves immediately. It is a
prompt-type hook, so it costs one fast-model evaluation per stop; if
that overhead bothers you in a project that rarely uses the pipeline,
disable the plugin's hooks in that project's settings.

## SubagentStop — verdict contract

Fires when agent-ops-reviewer finishes. Validates the output against
the verdict contract (exactly one of the four verdicts, counts for
blocking findings and non-blocking suggestions, or a gate-failure
notice). Non-conforming output is sent back to the reviewer with the
specific violation.

## Known gap (planned)

Write-scoping for agent-ops-maintain (blocking writes outside
health-reports/) is currently a prompt rule in the agent definition,
not a PreToolUse hook — hook matchers select on tool name, not on
which subagent is calling, so a naive hook would tax every Write/Edit
in every session. If the harness gains per-agent hook scoping (or
agent-level `hooks` frontmatter proves reliable), promote that rule to
a hook.

## Verifying hooks load

Run `/hooks` in a session with the plugin enabled — both entries
should be listed with their source shown as the agent-ops plugin.
