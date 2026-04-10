---
name: api-docs-coverage
description: >
  Identifies public functions, endpoints, and exports without
  documentation. Use monthly to catch gaps before they become
  tribal knowledge.
---

# API documentation coverage

## Setup

Read CLAUDE.md and package.json to detect the stack. Determine
where API definitions live (routes/, api/, src/lib/, etc.).

## What to check

### Exported functions without JSDoc/docstring [heuristic]
- Find all exported functions/classes in source files
- Check if each has a documentation comment (JSDoc, docstring,
  rustdoc, godoc) immediately above it
- Exclude: test files, internal/private functions, type exports
- Flag undocumented public exports as `warning`
- Prioritize: functions in files under api/, routes/, lib/, or
  explicitly public modules

### API endpoints without documentation [heuristic]
- Find route/endpoint definitions (Express routes, Next.js API
  routes, FastAPI endpoints, etc.)
- Check for accompanying documentation (inline docs, OpenAPI spec,
  or markdown docs referencing the endpoint)
- Flag undocumented endpoints as `warning`
- Flag endpoints handling auth or money as `critical`

### Type exports without descriptions [heuristic]
- Find exported TypeScript interfaces/types, Go structs, Python
  dataclasses, Rust pub structs
- Check for documentation comments on fields
- Flag complex types (>5 fields) without docs as `info`

## Report format

```
# API Docs Coverage — [project] — YYYY-MM-DD

## Critical
- [file:line] — [endpoint/function] handles [auth/payment] with no docs
  → Action: document parameters, return values, and error cases

## Warning
- [file:line] — [function/endpoint] exported without documentation
  → Action: add JSDoc/docstring with description and parameters

## Info
- Public exports: [n], documented: [n], undocumented: [n]
- API endpoints: [n], documented: [n], undocumented: [n]
- Coverage: [n]%
```
