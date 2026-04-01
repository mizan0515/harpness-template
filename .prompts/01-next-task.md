# Next Task — Plan and Execute — Codex Orchestrator Procedure

This prompt is read and executed by Codex. Codex selects the next task,
then delegates to Claude Code as Generator/Evaluator to complete it.

---

## Autonomous Continuous Execution Rules

**This procedure does not stop after one sprint.**
After PASS → commit/push, immediately return to step 1 and start the next task.
Do not wait for user re-confirmation.

Stop only when:
- User explicitly halts
- Document conflict requiring design decision
- Destructive git operation needed
- Unsafe git state (unrelated dirty files preventing clean commit)
- Codex token/time limit reached
- FAIL 3 times on the same item

Claude Code call policy (cost control — see AGENTS.md Cost Control Policy):
- Fixed prefix: `claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read CLAUDE.md and CLAUDE-HARNESS.md first. ..."`
- **effort `low` default** — `high` only for multi-file architectural refactoring or unknown-root-cause debugging.
  - Single file edits, data creation, research updates, test additions → never `high`.
- **fresh session default** — continued only for ≤1 file fix after immediate FAIL.
- **1-3 files per packet** target, max 5. Split if more.
- **Approval dialog → prefix approval** ("approve similar commands going forward").
- **Evaluator skippable**: doc-only fixes, research updates, comment/log changes, single typo → Codex verifies directly.
- **≤20 lines / ≤2 files obvious edits**: Codex does directly (zero Claude Code cost).
- One packet finishes → review result → immediately issue next packet.

Loop flow:
```
[1. Analyze] → [2. Candidates] → [3. Select] → [4. Contract] → [5. Generator]
  → [6. Review] → [7. Evaluator] → [8. Verdict: PASS→commit→1 / FAIL→fix→5]
  → repeat (no user wait)
```

---

## Current Milestone Context

{{MILESTONE_CONTEXT}}
<!-- Fill in your project's current state. Example:
  - Current milestone: MVP launch
  - Codebase: ~80 source files, 45 tests
  - UI: 8 of 14 pages implemented
  - Data: schema complete, seed data only
  - API: 24 endpoints live, 6 stubs
  - No E2E tests
-->

---

## 1. Current State Analysis (Codex direct)

- `git status`, `git log --oneline -10`
- Read project index document
- Check live source file inventory (do not trust docs alone)
- Compare implemented vs documented
- Check recent chat logs (if any)
- Check relevant `*-research.md` files

## 2. Candidate Collection and Reality Check (Codex direct)

- List key gaps for current milestone
- For each candidate, verify: actual file exists, runtime path works, tests exist, bindings present
- Exclude already-resolved items
- Split oversized items

## 3. Priority Decision and Select One Task (Codex direct)

Priority order:
1. Connecting work that closes the current milestone
2. Runtime paths that are actually broken
3. Missing bindings, wiring, or configuration
4. Core production code paths without tests
5. Incorrect completion status in docs/research
6. Content expansion

Task size limit:
- 1-3 source files / 1 schema / 1 UI screen / 1 API endpoint / 1 data migration

Exclude:
- Tasks too large to close in one sprint
- Tasks requiring design decisions not yet documented
- Large expansions not contributing to current milestone

## 4. Write Sprint Contract (Codex direct)

Per AGENTS.md Sprint Contract Format:
- Sprint Name, Goal, User-facing outcome
- In Scope, Out of Scope
- Files likely to change, Files that should not change
- Definition of Done
- Test Scenarios, Failure Conditions
- Risks / Assumptions
- Verification checks to perform

## 5. Issue Claude Code Generator Packet

Use 10-generator-packet.md template.
Packet opening: `Read CLAUDE.md and CLAUDE-HARNESS.md first. Then execute the Generator Packet below.`

Packet must include:
- Current state summary (5-10 lines)
- Sprint Goal, Required Outcome
- In/Out Scope
- Files allowed/avoided
- Constraints (from CLAUDE.md guardrails + 00-common-supplementary.md gotchas relevant to this task)
- Domain-specific rules/numbers
- Implementation notes
- Required self-check before handoff
- Handoff format

## 6. Review Generator Result (Codex direct)

- Changed files within scope?
- Out-of-scope changes justified?
- Reported risks reasonable?
- Research updated, verification performed?
- No guardrail violations?

## 7. Issue Claude Code Evaluator Packet

Use 11-evaluator-packet.md template.
Packet opening: `Read CLAUDE.md and CLAUDE-HARNESS.md first. Then execute the Evaluator Packet below.`

Packet must include:
- Sprint being evaluated
- Expected behavior
- Definition of Done (from step 4)
- Required test scenarios
- Explicit failure conditions
- Domain checklist items from 04-domain-checklists.md
- Verification checks
- Output format: PASS/FAIL + evidence

## 8. Verdict and Follow-up (Codex direct)

- PASS → commit/push → **immediately back to step 1** (no user wait)
- FAIL → write fix packet → back to step 5 (no user wait)
- FAIL on design issue → revise sprint contract → back to step 4
- FAIL 3x same item → stop and report to user

## 9. Sprint Memo (Codex direct, brief, before starting step 1 again)

- Completed task / changed files / verification result
- Remaining gaps (1-3)
- commit/push result
- (This memo is internal — do not wait for user response → proceed to step 1)
