# agent-ops

Autonomous software engineering pipeline for Claude Code. Agents
refine, plan, build, verify, and review your code — you make two
decisions: approve the plan and approve the code.

Zero runtime dependencies. Pure markdown that works wherever your
host tool runs. Stack-agnostic — configure your tools in CLAUDE.md.

## Usage

| I want to... | Run |
|--------------|-----|
| Build a feature end-to-end | `@agent-ops add push notifications` |
| Fix a bug with a reproducer test | `@agent-ops fix [bug description]` |
| Plan now, build later | `@agent-ops plan the auth migration` |
| Implement an approved plan | `@agent-ops implement plans/2026-04-06-auth.md` |
| Explore or refine an idea | `@agent-ops-refiner think through the API redesign` |
| Review code independently | `@agent-ops-reviewer review my PR, be brutal` |
| Run health checks | `@agent-ops-maintain run weekly checks` |
| Triage production errors | `@agent-ops-maintain triage errors` |

## Install

### Prerequisites

- [Claude Code](https://claude.ai/download) CLI or desktop app
- A project with a CLAUDE.md file (the agents read it for configuration)

### Quick start

```bash
# 1. Install the plugin
claude plugin marketplace add https://github.com/epzee/agent-ops
claude plugin install agent-ops

# 2. Add agent-ops sections to your project's CLAUDE.md
#    (copy from templates/CLAUDE-md-sections.md)

# 3. Verify it works
@agent-ops-refiner what does this project do
```

See [setup guide](docs/SETUP.md) for project configuration details.

### Other tools

Use the markdown body of any agent file as a system prompt.
The autonomous pipeline requires subagent support. Independent
reviewer context requires isolated subagent sessions.

## How agent-ops works

Five agents, six skills, one enforced pipeline. The coordinator runs
end-to-end; the specialists handle individual phases when called directly.

```
┌─────────────────────────────────────────────────────┐
│  agent-ops (coordinator)                            │
│                                                     │
│  ┌─ Entry points ──────────────────────────────┐    │
│  │ full pipeline · plan-only · implement-plan  │    │
│  └─────────────────────────────────────────────┘    │
│                                                     │
│  Skill discovery → load skills for current phase    │
│  Pipeline        → refine · plan · build · verify   │
│                    · simplify · review              │
│  Red-green       → failing test first, per task     │
│  Gate (enforced) → tests · typecheck · lint · build │
│  Circuit breaker → 3 fails → escalate with context  │
│  Decision points → 2 human approvals (plan, code)   │
└─────────────────────────────────────────────────────┘
```

**Key design choices:**

- **Gates are enforced, not advised.** Verification runs four commands
  from your CLAUDE.md. "Override" is the only escape hatch and it stamps
  the artifact so every downstream reviewer knows.
- **Red-green by default.** Build tasks write a failing test first,
  confirm it fails for the right reason, then implement. Carve-outs
  (UI polish, config, migrations, docs) are explicit.
- **Reviewers run in isolated contexts.** Independent review is a
  structural fix for anchoring bias, not a process preference.
- **Two decisions, not six.** Approve the plan, approve the code.
  Everything in between is automated.

<details>
<summary><b>Pipeline flow diagram</b></summary>

```mermaid
graph TD
    Input[Your idea]

    subgraph auto1 ["Define and Plan"]
        Refine[Refine] --> Plan[Plan] --> ReviewPlan[Review plan]
    end

    Input --> Refine
    ReviewPlan --> ApprovePlan{Approve plan?}
    ApprovePlan -->|No| Refine
    ApprovePlan -->|Yes| Build

    subgraph auto2 ["Build and Verify"]
        Build[Build<br/>red → green per task] --> Simplify[Simplify] --> Gate[Verify gate]
    end

    Gate -->|Fail| Build
    Gate -->|3 failures| Escalate[Escalate to you]
    Gate -->|Pass| ReviewCode[Review code]
    ReviewCode --> ApproveCode{Approve code?}
    ApproveCode -->|No| Build
    ApproveCode -->|Yes| Ship[Ship]

    Ship --> Maintain[Maintain]
    Maintain -.-> Input
```

</details>

<details>
<summary><b>Example runs</b></summary>

**Full pipeline**

```mermaid
sequenceDiagram
    You->>agent-ops: add push notifications
    Note right of agent-ops: Refine → Plan → Review plan
    agent-ops->>You: Plan + verdict (0 blocking)
    You->>agent-ops: go ahead
    Note right of agent-ops: Build → Simplify → Gate → Review code
    agent-ops->>You: Code + verdict (Ship it)
    You->>agent-ops: ship it
```

**Plan now, build later**

```mermaid
sequenceDiagram
    You->>agent-ops: plan the auth migration
    Note right of agent-ops: Refine → Plan → Review
    agent-ops->>You: Plan + verdict
    You->>agent-ops: approved
    Note right of agent-ops: Saved to plans/
    Note over You: Days later...
    You->>agent-ops: implement plans/2026-04-06-auth.md
    Note right of agent-ops: Build → Simplify → Gate → Review
    agent-ops->>You: Code + verdict
    You->>agent-ops: ship it
```

</details>

## Agents

Five agents: one coordinator plus four phase specialists. Call the
coordinator for end-to-end runs, or call a specialist directly.

| Agent | Role | Use When |
|-------|------|----------|
| [agent-ops](agents/agent-ops.md) | Coordinator — full Define → Plan → Build → Verify → Review pipeline | Building a feature end-to-end or implementing an approved plan |
| [agent-ops-refiner](agents/agent-ops-refiner.md) | Define — shapes rough ideas into precise, role-specific prompts | You have a vague idea and want it structured before planning |
| [agent-ops-planner](agents/agent-ops-planner.md) | Plan — creates structured plans with runnable verification steps | You have a clear prompt and need an executable task breakdown |
| [agent-ops-reviewer](agents/agent-ops-reviewer.md) | Review — independent reviews of plans, code, or tests | You want a second opinion from a context-isolated reviewer |
| [agent-ops-maintain](agents/agent-ops-maintain.md) | Maintain — hygiene checks and production error triage | Running weekly/monthly health checks or investigating errors |

## Skills

Skills are markdown files that agents load at runtime. They encode
workflows, output contracts, and quality gates.

| Phase | Skill | What It Does |
|-------|-------|--------------|
| Define | [refiner-roles](skills/refiner-roles.md) | Lookup table of engineering role perspectives for shaping ideas |
| Plan | [plan-format](skills/plan-format.md) | Output template for plans: task structure, verification, scope |
| Build | [test-first](skills/test-first.md) | Red-green loop: failing test first, confirm honest red, then green |
| Verify | [verification-gate](skills/verification-gate.md) | Four enforced checks: tests, typecheck, lint, build |
| Review | [review-criteria](skills/review-criteria.md) | Review type routing, intensity levels, and verdict output contract |
| Maintain | [maintenance-checks](skills/maintenance-checks.md) | Dispatches scheduled maintenance tasks and error triage |

Agents also discover skills from installed plugins at runtime — see
[Works great with](#works-great-with).

## Enforcement

```
▶ Running gate...
  ✅ Tests: 142 passed  ❌ Typecheck: 3 errors
  ⏹ FAILED. Fixing. (1 of 3)

▶ Re-running...
  ✅ Tests  ✅ Types  ✅ Lint  ✅ Build
  ✅ Passed. Proceeding to review.
```

3 failures → escalates to you with full context.

## Repo structure

```
├── agents/          5 agents — coordinator + 4 specialists
├── skills/          6 skills — gate, test-first, review, plan format, maintenance, roles
├── workflows/       4 workflows — feature, plan-only, add-tests, template
├── maintenance/     26 tasks by category
│   ├── code-health/   complexity, dead code, TODOs, deps, bundle
│   ├── security/      vulns, secrets, licenses, OWASP
│   ├── testing/       coverage, flaky, missing, lint drift
│   ├── production/    errors, perf, stale PRs, deploys
│   ├── ai-docs/       CLAUDE.md, skills, prompts, best practices, ecosystem
│   └── documentation/ README, API docs, changelog
├── templates/       CLAUDE.md sections for your project
└── docs/            setup, customizing, scheduled tasks, philosophy
```

## Works great with

**[agent-skills](https://github.com/addyosmani/agent-skills)** —
engineering skills for Define → Ship. Agents discover and use
these automatically at runtime, alongside any other installed
plugin skills. Recommended but not required — the pipeline works
standalone.

## Design

- **Markdown, not runtime.** No dependencies, no build step, no lock-in.
  Works wherever your host tool runs. Uninstall by deleting.
- **State in conversation.** Plans and reports are written to disk. The
  pipeline sequence (which phase is next, retry count) lives in
  conversation context — closing the conversation stops the pipeline.
  Resume by referencing the saved plan or report.
- **Claude Code-first.** The full autonomous pipeline needs subagent
  support. Independent review needs isolated contexts. In single-context
  tools, the reviewer shares the builder's thread.
- **Commands in CLAUDE.md.** The framework defines what to check;
  your project defines how. No hardcoded tool names.

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
