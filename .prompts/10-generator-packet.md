# Claude Code Generator Packet Template

This file is referenced by Codex when writing Generator bounded packets for Claude Code.
This is NOT directly fed to Claude Code.

---

## Default CLI Shape

```
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read CLAUDE.md and CLAUDE-HARNESS.md first. [packet]"
```

- effort `low` default. `high` only for multi-file architectural refactoring or unknown-root-cause debugging.
- fresh session default. continued only for ≤1 file fix after immediate FAIL.
- 1-3 files per packet target, max 5. Split if more.
- handoff context: 5-10 lines. Do not paste full file contents into prompt.
- Approval: prefer reusable prefix approval.
- Windows EPERM: not a Claude bug, escalate permissions and retry.

---

## New Session Packet Structure

```
Read CLAUDE.md and CLAUDE-HARNESS.md first.

## Context (current state 5-10 lines)
[Codex writes a brief summary of current repo state]
- Current milestone: {{CURRENT_MILESTONE}}
- [Task-relevant details]

## Generator Packet
- Sprint: [name]
- Goal: [one sentence]
- Required Outcome: [concrete deliverable]
- In Scope: [files/features]
- Out of Scope: [explicit exclusions]
- Files allowed to modify: [list]
- Files to avoid: [list]
- Constraints:
  - [CLAUDE.md guardrails relevant to this task]
  - [00-common-supplementary.md gotchas relevant to this task]
- Domain-specific rules:
  - [Key numbers, rules, or formulas for this domain]
- Implementation notes: [specific hints, current state of relevant files]
- Required self-check before handoff:
  1. Changed files within scope
  2. Research files updated (relevant *-research.md)
  3. Narrowest verification performed
  4. No guardrail violations
- Handoff format:
  - Mode used: Generator
  - Files changed
  - What was directly verified
  - What remains unverified
  - Risks / blockers
  - Suggested next packet
```

---

## Delta Packet (same-task short fix loop only)

```
Continue the current session. Do not rerun full repository audit.
Apply the delta packet below:

## Delta
- What to fix: [specific items from Evaluator]
- Files to touch: [list]
- What NOT to change: [maintain scope]
- Verify after fix: [narrowest check]
```

---

## Constraint Excerpt Guide (Codex reference)

When writing packets, include domain-relevant constraints:

{{CONSTRAINT_GUIDE}}
<!-- Map your project's domains to their key constraints.
  Example:
  **API work:**
  - All mutations through tRPC procedures
  - Auth middleware on protected routes
  - Error format must follow shared schema

  **UI work:**
  - Components must not call DB directly
  - Loading and error states required
  - Mobile-first responsive

  **Data work:**
  - Schema changes need migration
  - Seed data must cover test scenarios
  - No raw SQL outside repository layer
-->
