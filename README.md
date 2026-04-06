# agent-ops

A workflow and agent framework for planning, building, reviewing, and
maintaining software projects.

You describe what you want. The agents plan it, review the plan, build it,
verify it passes, review the code, and present you with the result. You
make two decisions: approve the plan, approve the code.

Designed for Claude Code. Usable as system prompts in other tools.
Stack-agnostic — configure your tools in CLAUDE.md.

## The lifecycle

```
 DEFINE       PLAN        BUILD       VERIFY      REVIEW       SHIP       MAINTAIN
┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐
│  Idea  │→ │  Spec  │→ │  Code  │→ │  Test  │→ │   QA   │→ │   Go   │→ │ Hygiene│
│ Refine │  │  Plan  │  │  Impl  │  │  Gate  │  │  Gate  │  │  Live  │  │ Triage │
└────────┘  └────────┘  └────────┘  └────────┘  └────────┘  └────────┘  └────────┘
                              ▲           │          │                        │
                              │    ◆ enforced  ◆ YOU approve           feeds back
                              └── fix & retry                       to DEFINE ──┘
```

```
1. Define        agent-ops-refiner      shapes your idea
   │
2. Plan          agent-ops-planner      creates structured plan
   │
3. Review plan   agent-ops-reviewer     reviews plan
   │
   ◆ YOU APPROVE THE PLAN
   │
4. Build         agent-ops              implements task by task
   │
5. Verify        gate (enforced)        tests, types, lint, build
   │             ↻ fix & retry (max 3)
   │
6. Review code   agent-ops-reviewer     independent code review
   │
   ◆ YOU APPROVE THE CODE
   │
7. Ship          you                    merge & deploy
   │
8. Maintain      agent-ops-maintain     hygiene checks + error triage
   └─── feeds back to step 1
```

## What it looks like

### Full pipeline

```
You: @agent-ops add push notification preferences

  [autonomous]
  → Refiner shapes idea → Planner creates plan → Reviewer reviews

You see: plan + verdict
  Verdict: Ready to implement
  Blocking findings: 0
  Non-blocking suggestions: 1
You: go ahead

  [autonomous]
  → Builds → Gate passes → Reviewer reviews code

You see: code + verdict
  Verdict: Ship it
  Blocking findings: 0
  Non-blocking suggestions: 1
You: ship it
```

### Enforcement

```
▶ Running gate...
  ✅ Tests: 142 passed  ❌ Typecheck: 3 errors
  ⏹ FAILED. Fixing. (1 of 3)

▶ Re-running...
  ✅ Tests  ✅ Types  ✅ Lint  ✅ Build
  ✅ Passed. Proceeding to review.
```

3 failures → escalates to you with full context.

### Maintain: two modes

```
@agent-ops-maintain run weekly checks
  → Runs tools, reads output, compares thresholds
  → 2 critical vulns, coverage below floor, 3 stale PRs

@agent-ops-maintain triage today's errors
  → Investigates: reads stack traces, finds code, correlates deploys
  → Critical: checkout crash since Friday deploy
  → Silence: analytics timeout, third-party noise
```

### Standalone agents

```
@agent-ops-refiner think through this API redesign
@agent-ops-planner plan the migration
@agent-ops-reviewer review my PR, be brutal
@agent-ops-maintain triage errors
```

## Install

### Claude Code (full experience)
```bash
claude plugins add https://github.com/epzee/agent-ops
```

### Other tools
Use the markdown body of any agent file as a system prompt.
The autonomous pipeline requires subagent support. Independent
reviewer context requires isolated subagent sessions.
See [setup guide](docs/SETUP.md).

## Skill discovery

Agents discover and load relevant skills at runtime from
.claude/skills/, installed plugins, and skill packs.

### Works great with
**[agent-skills](https://github.com/addyosmani/agent-skills)** —
engineering skills for Define → Ship. Agents discover and use
these automatically. Recommended.

## Limitations

- State lives in conversation. Resume to continue.
- Claude Code-first. Pipeline needs subagents.
- Independent review needs isolated subagent contexts.
  In single-context tools, reviewer shares thread with builder.
- Maintenance commands configured per-project in CLAUDE.md.
- Not a runtime. Markdown guiding the host tool.

## Docs

- [Setup](docs/SETUP.md) | [Customizing](docs/CUSTOMIZING.md)
- [Scheduled tasks](docs/SCHEDULED-TASKS.md) | [Philosophy](docs/PHILOSOPHY.md)

## Resources

- [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)
- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [Agent skills best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [The complete guide to building skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)
- [agent-skills](https://github.com/addyosmani/agent-skills) — engineering skills for Define → Ship

## License
MIT
