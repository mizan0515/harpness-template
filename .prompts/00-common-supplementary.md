# Common Supplementary Rules — CLAUDE.md Delta

Codex references this file and excerpts relevant items when writing Claude Code packets.
This file does NOT repeat content already in CLAUDE.md Part 1.

---

## Problem Grading

| Grade | Definition | Action |
|-------|-----------|--------|
| A | Compile/build error, test failure, spec rule violation, state ownership breach | Fix immediately |
| B | Completion status mismatch, binding gap, research inaccuracy, terminology mixup | Fix in same task |
| C | Wording polish, supplementary notes | Fix only if in scope |

---

## Repository Hidden Gotchas (not in CLAUDE.md)

{{REPO_GOTCHAS}}
<!-- List 10-20 gotchas specific to your project that AI agents frequently get wrong.
  Organize by category. Examples:

  ### Deleted Legacy Paths
  - `oldRouter` deleted. Use `newRouter` from `src/lib/router.ts`.
  - Legacy REST endpoints in `/pages/api/` are deprecated. Use tRPC.

  ### Partial Implementations / Stubs
  - `PaymentService` is a stub — Stripe integration not connected yet.
  - `SearchIndex` uses in-memory fallback. Elasticsearch not configured.

  ### Data Reality
  - `prisma/seed.ts` creates 3 sample users only. Production has 18 roles.
  - No authored product data in DB yet — all tests use fixtures.

  ### Test Reality
  - 45 unit tests passing. No integration/E2E tests.
  - Tests use mocked DB. No real database connections in CI.
-->

---

## AI Frequent Mistake Patterns (not in CLAUDE.md)

{{AI_MISTAKES}}
<!-- List 5-10 mistakes AI agents commonly make in your project.
  Examples:
  - Do not assume a component exists just because it is referenced in docs.
  - Do not treat fixture/seed data as production-complete.
  - Do not claim "tests pass" as full verification when only unit tests exist.
  - Do not use temp plans or chat logs as source of truth.
  - Schema existing does not mean data is populated.
-->

---

## Unimplemented Status Labels

When a large feature is not yet implemented, do not force-implement it. Use one of:
- `not implemented`
- `partially implemented`
- `follow-up needed`
- `verify against live repo`
- `needs design decision`

---

## Quick Reference Numbers

{{QUICK_REFERENCE}}
<!-- Table of key project numbers for packet writing.
  Example:
  | Item | Value |
  |------|-------|
  | DB models | 12 of 18 planned |
  | API endpoints | 24 live / 6 stub |
  | UI pages | 8 of 14 designed |
  | Test count | 45 unit / 0 E2E |
  | Supported locales | en, ko |
-->
