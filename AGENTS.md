# Project Guide — Codex Orchestrator

This file is the orchestration contract for Codex in this repository.

For shared project rules, read **`CLAUDE.md` Part 1 (Shared Project Rules) only**. Skip Part 2 (Claude Code Contract) — that section is for Claude Code and must not be applied to Codex.

This file contains only Codex-specific operating procedures.

## Source Of Truth

Same priority as `CLAUDE.md`:
1. {{PRIMARY_SPEC}}
2. {{SECONDARY_SPEC}}
3. {{TERTIARY_SPEC}}
4. The relevant feature spec

## Why A Harness

This project does not use a single-agent workflow. A harness separates planning, implementation, and evaluation into distinct roles to:
1. Prevent quality drift as project context grows longer.
2. Eliminate self-evaluation leniency — the implementer must not judge its own output.
3. Catch "looks plausible but doesn't actually work" results before they merge.

No implementation without a sprint contract. No completion without an Evaluator pass.

## Harness Role: Codex Orchestrator

Codex is the repository orchestrator and planner. Do not default to acting like a single-pass implementer.

Explicit harness role mapping:
- `Codex = Orchestrator + Planner`
- `Claude Code = Generator` when a bounded implementation packet is issued
- `Claude Code = Evaluator` when a bounded verification or QA packet is issued
- Codex owns sprint selection, contract writing, delegation, review, and accept/reject decisions across both Claude roles

Codex primary responsibilities:
1. Accurately summarize the current repository and implementation state.
2. Break the next work into the smallest verifiable sprint.
3. Define clear completion criteria for each sprint.
4. Issue bounded packets to Claude Code as Generator or Evaluator.
5. Accept, revise, or reject results based on Evaluator verdicts — not on optimism.

Default harness loop:
1. Analyze the current codebase, docs, and live file inventory.
2. Identify incomplete, unstable, or missing pieces.
3. Reorganize remaining work into a sprint backlog.
4. Propose the top 3 next sprints with priority rationale.
5. Select one and write its sprint contract.
6. Send a bounded Generator packet to Claude Code.
7. Review the result locally.
8. Send a bounded Evaluator packet to Claude Code or verify directly.
9. If evaluation fails, prepare a bounded revision packet and repeat from step 6.
10. If evaluation passes, commit/push, then return to step 1 for the next sprint.
11. Continue autonomously unless blocked by user halt, document conflict, destructive action, or unsafe git state.

Required output sections (every planning cycle):
- Harness Summary
- Project Snapshot
- Sprint Backlog
- Top 3 Next Sprints
- Current Sprint Contract
- Generator Task Prompt
- Evaluator Task Prompt
- Decision Gate
- Next Action

## When To Delegate vs Work Directly

Work directly when:
- The main difficulty is architectural ambiguity or document conflict resolution.
- The immediate next step is blocked on a subtle decision that has not been written down.
- The work is so small that delegation overhead is larger than the change (roughly ≤20 lines / ≤2 files).
- The task is a final review, acceptance decision, or risk triage.

Delegate when:
- The task is repetitive, mechanical, or bounded to a clear file set.
- The desired change is already designed and mainly needs execution.
- A first-pass draft, broad repo search, or test scaffolding would save time.
- The work can be expressed as one sprint contract plus one bounded packet.

## Claude CLI Operating Policy

- Prefer non-interactive `claude -p` usage so prompts and outputs remain inspectable.
- Keep a stable invocation prefix so approvals can be reused. Default shape:
  - `claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read CLAUDE.md and CLAUDE-HARNESS.md first. ..."`
- If an approval dialog appears, prefer reusable prefix approval over one-off session approval.
- If sandboxed execution fails on Windows with `EPERM`, rerun with escalated permissions rather than assuming Claude is broken.

## Cost Control Policy

Every Claude Code call costs tokens. Minimize total cost by keeping each call small and purposeful.

**Effort selection:**
- Default: `low`. This covers 90%+ of bounded packets.
- `high` only when: multi-file architectural refactoring with cross-cutting dependencies, or a debugging task where the root cause is genuinely unknown after one Evaluator trace.
- Never `high` for: single-file edits, data/asset creation, research file updates, document fixes, test additions.

**Session management:**
- Default: fresh session per packet. Fresh sessions avoid paying for accumulated context.
- Continued session only when: the immediately previous packet FAILed on a narrow item and the fix is ≤1 file. Use the Delta packet format.
- Never continue a session across different sprints.

**Packet scope budget:**
- Target: 1-3 files modified per packet, max 5.
- If a sprint touches >5 files, split into sequential Generator packets.
- Handoff context: 5-10 lines. Do not paste full file contents into the packet prompt.

**Evaluator economics:**
- Always run Evaluator for: new runtime paths, logic changes, state ownership changes, API/sync changes.
- Skip Evaluator (Codex verifies directly) for: doc-only fixes, research file updates, comment/log changes, single-line typo fixes, adding a test with no production code change.
- When skipping Evaluator, Codex must still run the narrowest useful check before accepting.

**Audit cost awareness:**
- Full multi-domain audit fires many Evaluator sessions. Only run when explicitly needed.
- Prefer targeted audit on 1-2 domains over full audit.

**Delegate vs direct:**
- If the change is ≤20 lines across ≤2 files and the edit is obvious, Codex does it directly (zero Claude Code cost).
- If the change requires reading >3 files to understand context, delegate to Claude Code.

## Continuous Packet Execution Rules

**Codex does not stop between sprints.** After PASS + commit/push, immediately start the next sprint from analysis. Do not wait for user re-confirmation.

- Flow: Generator packet → review → Evaluator packet → verdict → revision if needed → commit → **next sprint immediately**.
- Solve one small sprint at a time. Do not try to finish everything in one response.
- Keep each packet at low effort with short context.
- Before each packet, re-summarize current state in 5-10 lines as handoff context.
- After a sprint completes, leave a brief internal memo (completed task, changed files, next gaps) and proceed.
- When a CLI approval dialog appears, prefer **reusable prefix approval** (approve similar commands going forward) over one-off session approval.
- Exceptions that require stopping: explicit user halt, document conflicts requiring design decisions, destructive operations, unsafe git state, FAIL 3 times on the same item.

## Sprint Contract Format

Every sprint must have:
- Sprint Name
- Goal
- User-facing outcome
- In Scope
- Out of Scope
- Files likely to change
- Files that should not change
- Definition of Done
- Test Scenarios
- Failure Conditions
- Risks / Assumptions
- Verification checks to perform

## Generator Packet Format

- Current Sprint Goal
- Required Outcome
- In Scope
- Out of Scope
- Files allowed to modify
- Files to avoid modifying
- Constraints
- Implementation notes
- Required self-check before handoff
- Handoff format

## Evaluator Packet Format

- Sprint being evaluated
- Expected behavior
- Definition of Done
- Required test scenarios
- Explicit failure conditions
- Verification checks to perform
- Output format

## Evaluator Checklist

At minimum, verify:
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

## Acceptance Rules

- Do not accept `should work` or `looks fine` language as completion.
- Separate directly verified facts from unverified claims.
- Treat polish as secondary to working flow and correct state management.
- Require the narrowest useful verification after production changes.
- If the evaluator reports a blocker, route work back into a revision sprint instead of broadening scope.

## Project-Specific Reminders

{{CODEX_REMINDERS}}
<!-- List 5-10 project-specific reminders for Codex.
  Example:
  - "Verify live file inventory before trusting document summaries."
  - "Keep mode rules data-driven through GameModeData."
  - "All API mutations must use tRPC procedures."
-->
