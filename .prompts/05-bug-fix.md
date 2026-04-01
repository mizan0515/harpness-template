# Bug Reproduction and Fix — Codex Orchestrator Procedure

This prompt is read and executed by Codex. Define a bug, trace it with Claude Code, fix, and verify.

---

## 1. Define Bug (Codex direct)

- One-sentence bug definition
- Expected behavior (from which document/spec)
- Reproduction path hypothesis
- Related domain (reference 04-domain-checklists.md)

### Bug Classification Hints

| Category | Common Causes | Where to Look |
|----------|---------------|---------------|
| Build/compile error | Missing import, type mismatch | Build output, IDE errors |
| Runtime error | Null reference, unhandled exception | Error logs, stack trace |
| Logic error | Wrong calculation, incorrect condition | Service/business logic files |
| State error | State not synced, stale data | State management, API layer |
| UI error | Wrong display, broken interaction | Components, event handlers |
| Data error | Missing records, wrong schema | DB schema, seed data, loaders |
| Config error | Wrong env var, missing feature flag | Config files, deployment |
| Integration error | API contract mismatch, timing issue | API clients, webhooks |

## 2. Root Cause Trace (Claude Code Evaluator packet)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Act as Evaluator.
Bug: [definition]. Expected: [behavior]. Reproduce path: [hypothesis].
Trace the actual call sequence and identify root cause.
Classify: data / runtime logic / screen flow / state sync / binding / doc misunderstanding.
Check related domain checklist: [items from 04-domain-checklists.md]
Do NOT fix yet — report findings only.
```

## 3. Plan Fix (Codex direct)

- Review Evaluator result
- Determine smallest safe fix scope
- Define scope, files, constraints
- Check 00-common-supplementary.md for related gotchas
- Verify fix does not violate CLAUDE.md guardrails

## 4. Execute Fix (Claude Code Generator packet)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
Fix bug: [definition]. Root cause: [Evaluator result].
[scope, files, constraints, verification, handoff format]
```

Reference 10-generator-packet.md template.

## 5. Verify Fix (Claude Code Evaluator packet)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Evaluator Packet below.
Verify bug fix for: [definition].
[expected behavior, regression scenarios, failure conditions]
Ensure no regression in existing tests.
```

Reference 11-evaluator-packet.md template.

## 6. Closing (Codex direct)

- Bug definition / expected behavior / reproduction path / actual root cause
- Fix content / changed files / verification result / remaining risks
- Regression check: existing tests still pass
- commit/push status and reason
