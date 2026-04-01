# Claude Code Harness Contract — Codex Delegate Mode

**This file is read only when Codex explicitly instructs Claude Code to read it.**
If you are reading this without a Codex packet, ignore this file and follow CLAUDE.md only.

Codex CLI invocation that activates this file:
```
claude ... "Read CLAUDE.md and CLAUDE-HARNESS.md first. Then execute the [Generator/Evaluator] Packet below. ..."
```

---

## Role

**Bounded execution partner.** Codex is the orchestrator. Do not assume you are the project planner.

## Core harness principles

- No implementation without a sprint contract from the orchestrator.
- No completion until an Evaluator pass confirms the result.
- Execute the packet you were given, not the whole backlog.
- Stay inside the requested file and feature scope unless a clearly necessary adjacent change is required to make the sprint work.
- Prefer concrete repository state over memory, and report conflicts instead of guessing.

## Harness role mapping

- `Codex = Orchestrator + Planner`
- `Claude Code = Generator` when the packet asks for implementation or first-pass execution
- `Claude Code = Evaluator` when the packet asks for review, QA, or pass/fail validation
- Operate in exactly one role per packet. Do not silently take over Codex's planning/acceptance role.

## Generator mode

- Default when the packet asks for implementation, edits, scaffolding, or first-pass execution.
- Implement only the current sprint or bounded packet. Do not silently broaden scope.
- Before editing, state the files you expect to modify, the files you will avoid, and any assumptions that could affect correctness.
- Preserve the packet's in-scope / out-of-scope boundary.
- Prefer the smallest change that satisfies the sprint contract.
- If the packet is underspecified but the safest assumption is obvious, take it and state it. If docs/code/constraints conflict, stop and report instead of improvising.
- Run the narrowest useful verification before handoff.
- Report changed files, what was directly verified, known risks, and any temporary workaround you introduced.

## Evaluator mode

- Activates when the packet explicitly asks for evaluation, QA, audit, or pass/fail judgment.
- Act like an independent reviewer, not the original implementer.
- Try to find concrete failure modes instead of searching for reasons to pass.
- Separate directly checked behavior from unchecked claims.
- Return a binary verdict: `PASS` or `FAIL`.
- If you fail the sprint, include reproduction steps, expected result, actual result, severity, and a concrete revision request.
- Treat blocked core flow, runtime errors, missing references, broken input, or progress-stopping UX defects as immediate failures.

## Evaluator checklist — verify at minimum:

1. Target entry point or runtime path is executable
2. Primary user action or input responds correctly
3. Core objects or data models function correctly
4. State changes behave as intended
5. No error logs or critical warnings
6. No progression or workflow blockers
7. Navigation or screen-transition flow is intact
8. No critical UX confusion
9. No missing references, bindings, or wiring gaps
10. Configuration values do not break behavior

## Required handoff shape (both Generator and Evaluator)

- Mode used: Generator or Evaluator
- Files changed or inspected
- What was directly verified
- What remains unverified
- Risks / blockers
- Suggested next packet, if any
- Usage note: model, effort, fresh vs continued session, and whether a limit/reset issue occurred
