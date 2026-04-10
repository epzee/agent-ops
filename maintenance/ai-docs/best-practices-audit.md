---
name: best-practices-audit
description: >
  Detects the project's stack from config files and audits against
  framework-specific best practices. Use quarterly — best practices
  evolve slowly with major framework versions.
---

# Best practices audit

## Setup

Detect the stack by reading package.json, config files, and CLAUDE.md:

| Signal | Stack |
|--------|-------|
| `next.config.*` | Next.js |
| `expo` in deps | Expo / React Native |
| `react` in deps (no Next/Expo) | Plain React |
| `express`/`fastify`/`hono` in deps | Node API |
| `go.mod` | Go |
| `Cargo.toml` | Rust |
| `pyproject.toml`/`setup.py` | Python |

Apply the corresponding lens below. If multiple stacks: apply all.

## Stack-specific checks

### Next.js [heuristic]
- App Router vs Pages Router consistency
- Server/client component boundaries (unnecessary `"use client"`)
- Image optimization (next/image vs raw `<img>`)
- Font loading (next/font vs external CSS)
- Route segment config usage
- Metadata API for SEO

### React / React Native [heuristic]
- Stale patterns: class components, legacy lifecycle methods
- Hook rules compliance (no conditional hooks)
- Memoization overuse or underuse
- Key prop on lists (missing or index-based)
- State management patterns vs current recommendations

### Node API [heuristic]
- Error handling middleware presence
- Input validation at boundaries
- Rate limiting on public endpoints
- Graceful shutdown handling
- Environment variable validation at startup

### General [heuristic]
- TypeScript strict mode if TS is used
- ESM vs CJS consistency
- Monorepo structure if workspaces detected
- Lock file freshness (committed and up to date)

## Report format

```
# Best Practices — [project] — YYYY-MM-DD

## Critical
- [file:line] — [practice]: [finding]
  → Action: [specific update needed]

## Warning
- [file:line] — [practice]: [finding]
  → Action: [recommendation]

## Info
- Stack detected: [stack(s)]
- Framework version: [version]
- Checks applied: [n]
- Passing: [n], Failing: [n]
- Not applicable: [list with reasons]
```

Note: This is a heuristic scan, not a runtime audit. Flag
false-positive rate as moderate and recommend human review.
