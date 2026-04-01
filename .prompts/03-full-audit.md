# Full Code/Doc Audit — Codex Orchestrator Procedure

This prompt is read and executed by Codex. Broader than 02 — covers all domains,
including document contradictions, gaps, and structural issues.

---

## 1. Full Inventory Check (Codex direct)

Code inventory:
- List all source files by major directory
- {{SOURCE_DIRECTORIES}}
  <!-- Example:
  - `src/components/` — React components
  - `src/lib/` — shared utilities and services
  - `src/server/` — API and server-side code
  - `prisma/` — database schema and migrations
  -->

Doc inventory:
- All design/spec documents
- All research files (`*-research.md`)

Test inventory:
- Test files by type (unit / integration / E2E)
- Test count and pass rate

Data inventory:
- Schemas vs actual data (seed, fixtures, production)

## 2. Domain-by-Domain Evaluator Packets (Codex splits and delegates)

Extract relevant checkpoints from 04-domain-checklists.md for each domain.

{{AUDIT_DOMAINS}}
<!-- List your project's audit domains. Example:
  1. UI domain: wireframes vs implemented pages
  2. API domain: documented endpoints vs live routes
  3. Data domain: schema vs seed vs production data
  4. Auth domain: roles, permissions, session handling
  5. State domain: client state management, server state ownership
  6. Test domain: coverage gaps, mocked vs real
  7. Config domain: env vars, feature flags, deployment config
-->

Each domain Evaluator packet:
```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Act as Evaluator.
Audit [domain] for the following checklist:
[checkpoints from 04-domain-checklists.md]
[scope, expected rules, failure conditions]
Report: items checked, inconsistencies found, severity.
```

## 3. Collect and Classify Results (Codex direct)

- Gather all domain Evaluator results
- Grade each finding: A/B/C per 00-common-supplementary.md
- Determine fix priority

## 4. Execute Fixes (Claude Code Generator packets)

Bundle by grade, A-first. 1-3 fixes per packet.
Reference 10-generator-packet.md template.

## 5. Verify Fixes (Claude Code Evaluator packets)

Re-verify fixed domains via 11-evaluator-packet.md template.

## 6. Closing Report (Codex direct)

- Per-domain audit summary
- A/B/C findings list
- Fixed items
- Items left as "not implemented" or "needs design decision"
- Remaining risks
- commit/push status and reason
