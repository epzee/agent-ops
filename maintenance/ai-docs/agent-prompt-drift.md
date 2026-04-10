---
name: agent-prompt-drift
description: >
  Compares agent instructions against actual codebase conventions and
  flags misalignment. Use monthly to prevent slow divergence between
  what agents are told and what the code actually does.
---

# Agent prompt drift

## What to check

Read all agent definition files and compare their instructions
against the actual codebase state.

### Naming conventions [heuristic]
- Extract naming patterns agents are told to follow (e.g.,
  "use kebab-case for files", "PascalCase for components")
- Sample recent files and check if conventions match
- Flag mismatches as `warning`

### Directory conventions [heuristic]
- Extract directory structure references from agent prompts
- Compare against actual project structure
- Flag: agent told to put files in directories that don't exist
  as `critical`

### Tool/command assumptions [heuristic]
- Extract tool and command references from agent definitions
- Verify tools are available and commands work
- Flag unavailable tools as `warning`

### Workflow references [heuristic]
- Extract references to workflows, processes, or sequences
- Verify referenced workflow files exist and match descriptions
- Flag stale workflow references as `warning`

### Style and convention drift [heuristic]
- Compare agent coding style instructions against recent commits
- Look for patterns where agents are told one thing but the
  codebase consistently does another
- Flag systematic drift as `info` — may need to update either
  the agent prompt or the codebase

## Report format

```
# Agent Prompt Drift — [project] — YYYY-MM-DD

## Critical
- [agent file:line] — references [directory/tool] that doesn't exist
  → Action: update agent definition

## Warning
- [agent file:line] — convention [X] but codebase uses [Y]
  → Action: align agent prompt or codebase

## Info
- Agent files scanned: [n]
- Conventions checked: [n]
- Aligned: [n], Drifted: [n]
- Most common drift: [description]
```
