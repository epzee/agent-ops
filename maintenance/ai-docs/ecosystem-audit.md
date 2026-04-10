---
name: ecosystem-audit
description: >
  Verifies maintenance task dependencies are available and recommends
  skills, MCPs, and plugins the project would benefit from. Use monthly
  — tooling changes with dependency updates and new installations.
---

# Ecosystem audit

## 1. Task dependency check [tool-backed / heuristic]

Scan every `.md` file in maintenance/ subdirectories. For each task:
- Extract references to CLI tools (npm audit, depcheck, ts-prune, etc.)
- Extract references to MCPs (Sentry, GitHub, PostHog, Brave, etc.)
- Extract references to CLAUDE.md sections it reads (Maintenance commands, etc.)
- Verify availability: CLI tools via `which`, MCPs via settings.json
  and settings.local.json `mcpServers`, CLAUDE.md sections via reading

Produce a table: task name | dependency | status (available/missing).
Flag missing dependencies:
- `critical` if the task literally can't produce results without it
- `info` if the task can degrade gracefully (skip and note)

## 2. Stack-aware recommendations [heuristic]

Read package.json, CLAUDE.md, and config files to detect the stack.

### Skills
- Search installed plugins (`claude plugin list`) for relevant skills
- If `claude` CLI is not available, skip plugin search and note it
- Based on detected stack, recommend uninstalled skills that match:
  framework-specific practices, accessibility audits, performance tuning
- For each: name, what it adds, exact `claude plugin add` command

### MCP servers
- Cross-reference maintenance tasks against connected MCPs
- Recommend MCPs that would unblock or enhance existing tasks:
  error tracking, CI, analytics, search, database
- For each: name, which tasks it enables, exact `claude mcp add` command

### Plugins
- Check Anthropic and community marketplaces for plugin packs
  relevant to the detected stack
- For each: name, what it bundles, install command

## 3. AI docs alignment [heuristic]

Read CLAUDE.md, agent files, and skill files. For each reference
to a tool, MCP, or command:
- Verify the tool/MCP is actually installed and accessible
- Flag mismatches as `critical` — these cause silent agent failures
  where the agent looks wrong but is actually just misconfigured

## Report format

```
# Ecosystem Audit — [project] — YYYY-MM-DD

## Critical
- [task file] needs [tool/MCP] — not installed
  → Action: [install command] or disable task in schedules.md
- [CLAUDE.md:line] tells agent to use [tool] — not available
  → Action: install [tool] or remove instruction

## Warning
- [skill/MCP/plugin]: [what it adds]
  — Enhances: [which tasks or workflows]
  → Install: [exact command]

## Info
- [skill/MCP/plugin]: [what it adds]
  — Aligns with: [detected stack element]
  → Install: [exact command]

## Summary
- Tasks scanned: [n], fully runnable: [n], degraded: [n], blocked: [n]
- MCPs connected: [n], recommended: [n]
- Skills installed: [n], recommended: [n]
```
