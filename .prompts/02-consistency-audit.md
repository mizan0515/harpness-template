# Doc↔Code Consistency Audit and Fix — Codex Orchestrator Procedure

This prompt is read and executed by Codex. Systematically find and fix inconsistencies between documentation and implementation.

---

## Absolute Goal

Restore consistency. Do NOT change design intent.

---

## Known Risk Areas

{{CONSISTENCY_RISKS}}
<!-- Table of areas where doc and code frequently diverge.
  Example:
  | Area | Doc State | Code State | Common Mismatch |
  |------|-----------|------------|-----------------|
  | API endpoints | 30 documented | 24 live | Stubs marked as "done" |
  | DB models | 18 in spec | 12 in Prisma | Missing models claimed complete |
  | UI pages | 14 in wireframes | 8 implemented | Skeleton pages = "implemented" |
-->

---

## 1. Define Audit Scope (Codex direct)

- Full audit or targeted system audit?
- Specify documents and code to compare
- Confirm document priority order from CLAUDE.md

## 2. Collect Inconsistencies (Codex direct or Claude Code Evaluator)

For large scope, delegate to Claude Code Evaluator:
```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Act as Evaluator.
Audit the following scope for doc-code inconsistencies.
[scope, expected rules, failure conditions]
```

Types of inconsistency to find:
1. Doc says "done" but file/path/test/binding does not exist
2. Doc says "not implemented" but implementation exists
3. Doc terminology/numbers/rules differ from code
4. Research file lists/roles/gaps do not match actual files
5. State snapshot/restore fields missing for new additions
6. Parent doc conflicts with child doc
7. Decisions only in temp plans, not reflected in parent docs

## 3. Classify and Plan Fixes (Codex direct)

Apply 00-common-supplementary.md problem grading:
- A (fix now): Build errors, test failures, spec violations, state boundary breaches
- B (fix in same task): Status label errors, binding gaps, research mismatches, terminology
- C (if in scope): Wording, supplementary notes

Large unimplemented features → do NOT force-implement. Label accurately:
`not implemented` / `partially implemented` / `follow-up needed` / `verify against live repo` / `needs design decision`

## 4. Execute Fixes (Claude Code Generator packets)

Bundle 1-3 inconsistencies per packet:
```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
Fix the following doc-code inconsistencies: [list]
[scope, files, constraints, verification]
```

Reference 10-generator-packet.md template.

## 5. Verify Fixes (Claude Code Evaluator packets)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Evaluator Packet below.
Verify consistency fixes from Sprint [name].
[expected state, failure conditions, checks]
```

Reference 11-evaluator-packet.md template.

## 6. Closing Report (Codex direct)

- Inconsistency list (item / reference doc / actual state / grade / resolution)
- Changed code/doc files
- Items left as "not implemented" or "needs design decision"
- Remaining consistency risks
- commit/push status and reason
