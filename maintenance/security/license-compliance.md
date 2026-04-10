---
name: license-compliance
description: >
  Checks dependency licenses for incompatibilities with the project's
  license. Use monthly — licenses change with dependency updates, not
  daily.
---

# License compliance check

## Setup

Read the project's LICENSE file to determine its license type.
Read package.json `license` field. Detect package manager.

## What to check

### Dependency licenses [tool-backed / heuristic]
- If a license checker is available (license-checker, pip-licenses,
  go-licenses, cargo-license): run it
- Otherwise: read license fields from package metadata

### Compatibility classification
- `critical`: Copyleft licenses (GPL, AGPL, SSPL) in a non-copyleft
  project — these can force relicense of the entire project
- `warning`: Weak copyleft (LGPL, MPL, EPL) — compatible with care
  but may have linking/distribution requirements
- `info`: Permissive (MIT, BSD, Apache-2.0, ISC) — no issues

### Missing licenses
- Flag packages with no license declared as `warning`
- Flag packages with `UNKNOWN` license as `warning`

### License file presence
- Check that the project itself has a LICENSE file
- Check that LICENSE matches package.json `license` field

## Report format

```
# License Compliance — [project] — YYYY-MM-DD

## Critical
- [package@version]: [license] — incompatible with project [license]
  → Action: find alternative package or get legal review

## Warning
- [package@version]: [license] — weak copyleft, review obligations
  → Action: verify compliance with [license] terms
- [package@version]: no license declared
  → Action: check upstream, consider replacing

## Info
- Total packages: [n]
- Permissive: [n], Weak copyleft: [n], Copyleft: [n], Unknown: [n]
- Project license: [license]
```
