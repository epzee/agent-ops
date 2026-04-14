# Philosophy

## The lifecycle at a glance

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

### Step by step

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
5. Simplify      3 parallel agents      reuse, quality, efficiency review + fix
   │
6. Verify        gate (enforced)        tests, types, lint, build
   │             ↻ fix & retry (max 3)
   │
7. Review code   agent-ops-reviewer     independent code review
   │
   ◆ YOU APPROVE THE CODE
   │
8. Ship          you                    merge & deploy
   │
9. Maintain      agent-ops-maintain     hygiene checks + error triage
   └─── feeds back to step 1
```

## Why workflow framework, not engine

Plain markdown files, no runtime. The host tool (Claude Code or another
LLM tool) is the engine. agent-ops provides structure: which phases
exist, what each agent does, what gates must pass, when humans decide.
This means zero dependencies, instant uninstall, and portability
across tools.

## Why Maintain

CI catches per-PR issues. Nobody automates the slow rot: dependency
drift, coverage erosion, stale branches, config drift, secret exposure
over time. Maintenance is the phase between "shipped" and "next feature"
that most teams skip until something breaks.

## Why two maintain modes

**Maintenance (operator):** Run tools, read output, compare thresholds.
Deterministic. The agent doesn't analyze code — it runs the configured
tool and reports what it says. This keeps signal reliable and noise labeled.

**Triage (investigator):** Read stack traces, find source files, check
git history, correlate with deploys. This IS analysis. Different skill,
different philosophy, same agent with a mode switch.

Combining them would compromise both: maintenance would become
speculative, triage would become superficial.

## Why enforced gates with escape hatch

Gates are strict by default because "I'll fix it later" is how broken
code ships. But sometimes the gate fails on a pre-existing issue
unrelated to your change. The escape hatch requires the explicit word
"override" and stamps the artifact so every downstream reviewer knows.
The stamp persists — you can't quietly skip verification.

## Why red-green

Post-hoc tests catch regressions but miss three LLM failure modes.
First, **drift**: the implementation diverges from the intent because
the intent was never pinned as an executable spec. Second, **tautology
tests**: tests written after code pass trivially because they encode
what the code does, not what the task asks for. Third, **regressions
without reproducers**: a bug fix lands without a failing test that
would catch the bug coming back.

Red-green solves all three by forcing the acceptance criteria into a
failing test before any implementation exists. The builder confirms
the test fails for the right reason (assertion mismatch, not import
error), then writes the minimum code to pass. Carve-outs (UI polish,
config, migrations, docs) are named explicitly so the default is
"test-first" rather than "test-first when convenient." See
skills/test-first.md.

Codebases with no test harness get a Phase 0 bootstrap task — install
the runner, wire CLAUDE.md, add one smoke test — before any feature
work. Bootstrap once, then red-green forever.

## Why independent reviewers

When a reviewer sees the builder's reasoning, they anchor to it. They
find fewer issues and rate code higher than independent reviewers do.
Spawning the reviewer in a separate context (no access to builder's
conversation) is a structural fix for anchoring bias, not a process
preference.

In single-context tools, this guarantee doesn't hold — the reviewer
shares the thread with the builder. agent-ops is honest about this
limitation rather than pretending it doesn't matter.

## Why circuit breakers

Without a limit, an agent will retry indefinitely, burning tokens and
user patience. 3 retries is enough to catch transient issues and simple
fixes. After 3, the problem is likely structural. The escalation bundle
(artifact + findings + attempt history + unresolved questions) gives
the human everything needed to make a decision.

## Why run tools, not analyze

A real vulnerability scanner is better than an LLM tracing import
graphs. The tool has the vulnerability database; the LLM doesn't.
The maintenance agent's job is to run the right tool, read its output,
and compare against thresholds — not to replicate what the tool does.
This is why checks are labeled [tool-backed] vs [heuristic]: so humans
know which signals to trust.

## Why commands live in CLAUDE.md

Specific beats vague. "Run npm audit" is actionable. "Run your
dependency scanner" requires the agent to figure out what that means.
But hardcoding tool names in the framework couples it to one stack.

The solution: each project defines its commands in CLAUDE.md under
`## Maintenance commands`. The framework defines what to check
(vulnerabilities, outdated deps, dead code) and the project defines
how (npm audit, pip-audit, govulncheck). Same pattern as the
verification gate, which already reads test/build/lint commands
from CLAUDE.md.

## Standalone vs skill packs

**Standalone:** Pipeline structure, gates, maintenance, triage. Plans
formatted correctly with verification steps. Reviews cover essentials.
This is sufficient for many projects.

**With agent-skills:** Agents discover and load deeper methodology
at each phase. The refiner uses idea-refine techniques. The planner
uses planning-and-task-breakdown. The reviewer uses code-review-and-quality.
Recommended for best experience, but not required.

## State management

State lives in conversation context. This is a real limitation:
closing the conversation loses pipeline state. agent-ops is honest
about this rather than building a fragile persistence layer. Plans
persist as files. Reports persist as files. The pipeline sequence
is the part that requires an active conversation.

## Why honest about limits

- Claude Code-first: the autonomous pipeline needs subagents.
- No state persistence: conversation context is the session.
- Standalone is lighter: without skill packs, reviews are less deep.
- Independence needs subagents: single-context tools can't isolate the reviewer.

Claiming these don't matter would erode trust. Naming them lets users
make informed decisions about their setup.
