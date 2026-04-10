---
name: owasp-surface
description: >
  Scans for OWASP Top 10 vulnerability patterns in application code.
  Use quarterly — findings are structural and the analysis is expensive.
---

# OWASP Top 10 surface check

## Setup

Read CLAUDE.md and package.json to detect the stack. This determines
which patterns to scan for (e.g., SQL injection in a Node API vs
XSS in a React app).

## What to check

Scan source files for patterns associated with each applicable OWASP
category. Label all findings as `[heuristic]` — this is pattern
matching, not runtime analysis.

### A01: Broken Access Control
- Routes/endpoints without auth middleware
- Missing permission checks on sensitive operations
- Direct object references without ownership validation

### A02: Cryptographic Failures
- Hardcoded encryption keys or salts
- Use of weak algorithms (MD5, SHA1 for security)
- HTTP URLs for sensitive data transmission

### A03: Injection
- String concatenation in SQL queries (not parameterized)
- Unsanitized user input in shell commands (`exec`, `spawn`)
- Template injection patterns (`eval`, `dangerouslySetInnerHTML`)

### A05: Security Misconfiguration
- Debug mode enabled in production config
- Default credentials in config files
- CORS set to `*` in production
- Verbose error messages exposing internals

### A07: Auth Failures
- Session tokens in URLs
- Missing rate limiting on auth endpoints
- Weak password validation patterns

### A09: Logging Failures
- Sensitive data in log statements (passwords, tokens, PII)
- Missing audit logging on admin operations

### Omitted categories
- **A04 (Insecure Design):** design-level concern, not detectable by static pattern scanning
- **A06 (Vulnerable Components):** covered by `security/vulnerability-scan.md`
- **A08 (Software/Data Integrity):** design-level concern, not detectable by static pattern scanning
- **A10 (SSRF):** design-level concern, not detectable by static pattern scanning

## Report format

```
# OWASP Surface — [project] — YYYY-MM-DD

## Critical
- [file:line] — [OWASP category]: [finding]
  → Action: [specific remediation]

## Warning
- [file:line] — [OWASP category]: [finding]
  → Action: [recommendation]

## Info
- Categories scanned: [list]
- Categories not applicable: [list with reason]
- Stack detected: [stack]
```

All findings are heuristic. Flag false-positive rate as high and
recommend human review of criticals before acting.
