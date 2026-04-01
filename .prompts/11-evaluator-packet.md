# Claude Code Evaluator Packet Template

This file is referenced by Codex when writing Evaluator bounded packets for Claude Code.
This is NOT directly fed to Claude Code.

---

## Default CLI Shape

```
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read CLAUDE.md and CLAUDE-HARNESS.md first. [packet]"
```

Options same as 10-generator-packet.md.

---

## Packet Structure

```
Read CLAUDE.md and CLAUDE-HARNESS.md first.

## Context (current state 5-10 lines)
[Codex writes a brief summary of current state]
- Current milestone: {{CURRENT_MILESTONE}}
- Test baseline: {{TEST_BASELINE}} (e.g., "45 unit tests, all green")
- [Evaluation-relevant details]

## Evaluator Packet
- Sprint being evaluated: [name]
- Expected behavior: [concrete expected outcome]
- Definition of Done: [from sprint contract, verbatim]
- Required test scenarios:
  1. [scenario 1]
  2. [scenario 2]
- Explicit failure conditions:
  - Existing tests fail → immediate FAIL
  - Build/compile errors → immediate FAIL
  - [domain-specific conditions]
- Domain checklist (from 04-domain-checklists.md):
  - [checkpoints relevant to this sprint's domain]
- Verification checks:
  - {{VERIFICATION_STEPS}}
    <!-- Example (web): npm run typecheck → npm run test → npm run lint
         Example (Unity): mcp refresh → read console → run tests → get results
         Example (mobile): tsc --noEmit → jest --changedSince -->
- Output format:
  - Verdict: PASS or FAIL
  - Directly verified items
  - Unchecked items
  - If FAIL:
    1. Reproduction steps
    2. Expected result
    3. Actual result
    4. Severity (blocker / major / minor)
    5. Concrete revision request
```

---

## Evaluator Rules (CLAUDE-HARNESS.md auto-applied, packet can reinforce)

- Independent reviewer stance — not the original implementer
- Prioritize actual behavior over appearance
- "should work" is not evidence
- Distinguish minor issues from critical blockers
- Blocked core flow, runtime errors, missing references, broken input, crashes → immediate FAIL

## Domain-Specific FAIL Criteria (Codex selects relevant items)

{{DOMAIN_FAIL_CRITERIA}}
<!-- Define per-domain FAIL criteria. Example:

  **API FAIL:**
  - Unprotected endpoint that should require auth
  - Business logic computed in route handler instead of service
  - Response schema does not match documented contract

  **UI FAIL:**
  - Page unreachable from normal navigation
  - Form submits without validation
  - Error state not handled (silent failure)

  **Data FAIL:**
  - Migration breaks existing data
  - Seed data causes constraint violations
  - Schema field mismatch with spec
-->
