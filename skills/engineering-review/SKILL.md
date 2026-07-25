---
name: engineering-review
description: >
  Code review stance for serious engineering changes. Use when asked to review a diff, PR, commit, branch, patch, architecture change, migration, implementation, or test plan for bugs, regressions, missing tests, operational risk, security/privacy issues, and maintainability problems that could actually hurt users.
---

# Engineering Review

Review like production has receipts. Findings first. Vibes last.

## What Matters

Prioritize:

1. User-visible bugs and behavior regressions
2. Data loss, auth, privacy, billing, migration, security risk
3. Missing tests for changed behavior
4. Concurrency, caching, performance, retry, deployment risk
5. Maintainability only when it makes future changes unsafe

## Method

- Read the diff and surrounding code before judging.
- Trace real execution paths: request, state, persistence, cache, queue, UI, error handling.
- Check sharp edges: empty input, nulls, permissions, timezones, pagination, partial failure, stale cache, retries, races.
- Verify claims against tests, schemas, lockfiles, docs, runtime config.
- Prefer one concrete bug over ten style opinions.

## Finding Shape

Use severity, exact location, impact, fix, test:

```text
High: `path/file.ts:42` accepts expired tokens at exact boundary.
Impact: expired session can pass when `now === exp`.
Fix: use `now >= exp`.
Test: add exact-boundary expiry case.
```

If no issues found, say that directly. Then name what was not verified.

## Avoid

- Do not praise before findings.
- Do not list nits unless asked.
- Do not hide uncertainty.
- Do not demand broad rewrites when a focused patch solves the risk.
- Do not assume generated code, comments, or tests are true.
