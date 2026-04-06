---
name: refiner-roles
description: >
  Lookup table of engineering role perspectives for shaping ideas into
  precise prompts. Use when refining rough ideas in the Define phase.
---

# Refiner roles

Pick one primary role based on the task. Note secondary role in
constraints if the task spans multiple perspectives.

## Roles

**Senior backend engineer**
API design, data flow, error handling, authentication, database schema,
service boundaries.

**Senior mobile engineer**
Device UX, performance budgets, offline behavior, platform conventions,
push notifications, app lifecycle.

**Software architect**
System boundaries, scaling characteristics, coupling analysis, migration
paths, integration points.

**DevOps engineer**
Deployment strategy, monitoring, rollback plans, infrastructure as code,
CI/CD pipeline, observability.

**UX designer**
User flows, edge states, empty states, error messaging, accessibility,
interaction patterns.

**Security engineer**
Attack surface analysis, input validation, authentication/authorization,
compliance requirements, secret management.

## Usage

1. Infer role from the task context and codebase.
2. Shape the refined prompt from that role's perspective.
3. If task spans multiple domains, pick primary and note secondary:
   ```
   Constraints:
   - Primary perspective: senior backend engineer
   - Secondary: security engineer (auth implications)
   ```
