# Data Asset and Schema Validation — Codex Orchestrator Procedure

This prompt is read and executed by Codex. Validate data assets and schemas for consistency, then fix.

---

## Data Domain Status

{{DATA_STATUS}}
<!-- Table of your project's data domains.
  Example:
  | Domain | Documented | Target | Actual |
  |--------|-----------|--------|--------|
  | DB models | 18 | 18 | 12 (Prisma) |
  | API endpoints | 30 | 30 | 24 live + 6 stub |
  | Seed users | 50 | 10 (dev) | 3 |
  | Product catalog | 500 | 20 (dev) | 0 |
  | Feature flags | 12 | 12 | 8 |
-->

---

## 1. Define Validation Scope (Codex direct)

- Identify target data domain
- Check documented totals/rules
- Reference 04-domain-checklists.md data section

## 2. Collect Current Status (Claude Code Evaluator packet)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Act as Evaluator.
Audit data assets and schema for [domain].
Compare: documented totals vs actual totals (see status table for reference).
Check: schema fields, loader/repository paths, UI connections, type distinctions.
[scope, expected rules, failure conditions]
Report: coverage gaps, mismatches, missing loaders or UI bindings.
```

## 3. Classify and Plan Fixes (Codex direct)

- Grade per 00-common-supplementary.md: A/B/C
- Separate: schema issues, loader issues, UI binding issues
- Assess actual data coverage
- Fix scope: schema/loader/UI checked together

Key distinctions:
- Schema existing ≠ data populated
- Fixture/seed data ≠ production-complete
- Default/fallback values ≠ authored content

## 4. Execute Fixes (Claude Code Generator packet)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
Fix data/schema issues: [list].
[scope, files, constraints, verification]
```

Reference 10-generator-packet.md template.

## 5. Verify Fixes (Claude Code Evaluator packet)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Evaluator Packet below.
Verify data/schema fixes for [domain].
[expected state, failure conditions, checks]
```

Reference 11-evaluator-packet.md template.

## 6. Closing (Codex direct)

- Documented vs actual totals comparison
- Changed schema/loader/UI files
- Data coverage status
- Remaining data gaps
- commit/push status and reason
