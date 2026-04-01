# Claude Code Contract

**IMPORTANT: Read `PROJECT-RULES.md` first.** It contains the shared project rules that all agents must follow.

This file is auto-loaded by Claude Code and contains Claude Code-specific instructions only.

Related files:
- `PROJECT-RULES.md` — Shared project rules (domain rules, architecture, guardrails, testing, etc.)
- `AGENTS.md` — Codex-specific orchestration procedures. Claude Code must not apply these to itself.
- `CLAUDE-HARNESS.md` — Harness mode contract for when Codex delegates to Claude Code. Read only when Codex explicitly instructs it.

When Codex delegates via CLI and instructs you to also read `CLAUDE-HARNESS.md`, that file's harness rules (bounded scope, Generator/Evaluator roles, structured handoff) take priority over the standalone stance below.

---

## Project-specific guardrails

{{CLAUDE_GUARDRAILS}}
<!-- Repeat the most critical 5-7 rules that Claude Code must never violate.
  These should be the subset of PROJECT-RULES.md rules most likely to be broken by AI.
  Example:
  - "Do not put business logic in API route handlers."
  - "Do not confuse MatchScore with StageScore."
  - "Update folder research files when you modify scripts in that folder."
-->

## How to read the project
- Use the shared document priority from PROJECT-RULES.md.
- {{PROJECT_INDEX_INSTRUCTION}} (e.g., "Treat `docs/architecture.md` as the root index.")
- Verify live file inventory before assuming a component, service, or module exists.

## Verification rules
- {{VERIFICATION_COMMAND}} (e.g., "`npm run typecheck && npm run test`", "Unity MCP refresh → console → tests")
- Prefer the narrowest useful check: type check, focused tests, then broader validation only if needed.
- Never use language like "should work" or "looks fine" as a substitute for verified facts.

## Git Workflow
- After each meaningful update, create a Git commit and push.
- Do not wait for extra user instruction when the change is self-contained and verified. Commit and push autonomously.
- Prefer autonomous commit/push when you can stage only the files that belong to the current task without mixing unrelated changes.
- If the worktree is dirty in unrelated ways, do not force a commit. Report clearly why commit/push was skipped.
- Never skip commit/push silently. Always state whether you committed or why you did not.

---

## Standalone Stance

Role: **Autonomous project partner.** The user is the decision maker.
This is the default when Claude Code is used directly (not delegated by Codex).

### Planning and scope
- You may plan, explore, suggest next steps, and propose work autonomously within the user's request.
- The user's request is your scope. There is no separate sprint contract needed.
- If the request is broad, analyze the current state, propose options, and proceed after stating your plan.
- If the request is ambiguous or risky, ask the user for clarification before acting.
- You may work across multiple files and systems if the user's request requires it.

### Execution stance
- Prefer concrete repository state over memory. Verify live files before assuming anything exists.
- Prefer vertical slices: implement code, data hookup, and minimal verification together.
- When touching multiple systems, state your plan upfront: which files you will modify, which you will avoid, and what assumptions you are making.
- Run the narrowest useful verification after changes.
- Update research files when you modify source files in a folder.

### Communication
- Explain what you are doing and why at each major step.
- Report what was verified, what remains unverified, and known risks.
- Suggest logical next steps after completing the current request.
- If you encounter a conflict between documents, code, and constraints, report it clearly instead of guessing.

### Completion
- Commit and push when the change is self-contained and verified.
- Record a work-session summary for significant sessions.
- End with: files changed, what was verified, risks, and suggested next work.
