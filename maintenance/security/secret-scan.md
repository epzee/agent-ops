---
name: secret-scan
description: >
  Scans for hardcoded secrets, API keys, tokens, and credential leaks
  in source and config files. Use weekly — one leaked key can compromise
  everything.
---

# Secret / credential scan

## What to check

### Pattern scan [heuristic]
- Grep for common secret patterns in source files:
  - API keys: `AKIA[0-9A-Z]{16}`, `sk-[a-zA-Z0-9]{20,}`
  - Tokens: `ghp_`, `gho_`, `github_pat_`, `xoxb-`, `xoxp-`
  - Generic: `password\s*=\s*["'][^"']+`, `secret\s*=\s*["'][^"']+`
  - Base64 blobs: long base64 strings in non-test files
  - Private keys: `-----BEGIN.*PRIVATE KEY-----`
- Exclude: node_modules, vendor, .git, lock files, test fixtures
  explicitly named as test data

### Env file hygiene [heuristic]
- Check: `.env` files not in `.gitignore`
- Check: `.env` files committed to git history (`git log --all -- .env*`)
- Check: `.env.example` contains actual values instead of placeholders
- Flag any env file with real credentials as `critical`

### Config file scan [heuristic]
- Scan common config files for hardcoded values:
  database connection strings, webhook URLs with tokens,
  service account JSON files
- Flag as `critical` if values look real (not placeholder/example)

## Report format

```
# Secret Scan — [project] — YYYY-MM-DD

## Critical
- [file:line] — [pattern type]: [redacted preview]
  → Action: rotate credential immediately, remove from source

## Warning
- [file] — .env file not in .gitignore
  → Action: add to .gitignore, verify not committed

## Info
- Files scanned: [n]
- Patterns checked: [n]
- False positives likely: [list any uncertain matches]
```

**Important:** Redact actual secret values in the report. Show only
enough context to identify the finding (first 4 chars + `***`).
