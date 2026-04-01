# Project Guide

This file has two parts:
- **Part 1: Shared Project Rules** — applies to all agents (Codex, Claude Code, etc.)
- **Part 2: Claude Code Contract** — applies only to Claude Code. Codex must skip Part 2.

Related files:
- `AGENTS.md` — Codex-specific orchestration procedures. References Part 1 only.
- `CLAUDE-HARNESS.md` — Harness mode contract for when Codex delegates to Claude Code. Read only when Codex explicitly instructs it.

Claude Code reads this file automatically. When Codex delegates via CLI, it also instructs Claude Code to read `CLAUDE-HARNESS.md`, which overrides Part 2's standalone stance with bounded harness rules.

---

# Part 1: Shared Project Rules

## Source Of Truth

If documents conflict, use this priority order:
1. {{PRIMARY_SPEC}}
2. {{SECONDARY_SPEC}}
3. {{TERTIARY_SPEC}}
4. The relevant feature spec

Additional rules:
- Lower-priority documents must not redefine core numbers, rule ownership, or canonical terms from higher-priority documents.
- If a lower-priority summary is stale, follow the higher document and update the stale summary in the same task when possible.

## Core Project Facts

- Project type: {{PROJECT_TYPE}} (e.g., "2v2 team roguelite deckbuilding card game in Unity", "Next.js SaaS dashboard", "React Native mobile app")
- Tech stack: {{TECH_STACK}}
- Current implementation target: {{CURRENT_TARGET}} (e.g., "first playable slice", "MVP launch", "beta release")
- Current practical milestone: {{CURRENT_MILESTONE}}

## Current Repository Reality

Read this section before trusting older summaries, chat logs, or temp plans.

{{REPO_REALITY}}
<!-- List 5-15 bullet points about:
  - What exists vs what the docs say
  - Partial implementations and their actual state
  - Stale summaries that describe future modules as missing when files now exist
  - Key architectural decisions already made
  Example:
  - Authentication uses NextAuth with Google/GitHub providers. Email/password is not implemented.
  - The API layer uses tRPC. REST endpoints in /pages/api/ are legacy and should not be extended.
  - Database schema is defined in Prisma. 12 of 18 planned models exist.
-->

## Important Rules To Preserve

{{DOMAIN_RULES}}
<!-- List 5-15 project-specific invariants that agents must not break.
  Example (web):
  - All API mutations must go through tRPC procedures, not direct DB calls from components.
  - User roles are: admin, member, viewer. Do not invent new roles.
  - Billing state is owned by Stripe webhooks. Do not write billing logic outside the webhook handler.
  Example (game):
  - Team gold is shared. Do not introduce personal ownership semantics.
  - Events use exactly 1 primary type plus 0-2 secondary tags.
  Example (mobile):
  - Offline-first: all reads must work from local SQLite. Network is optional.
  - Push notification tokens are managed by the NotificationService singleton only.
-->

## Architecture And Data Rules

{{ARCHITECTURE_RULES}}
<!-- List rules about data flow, state ownership, and system boundaries.
  Example:
  - State ownership boundaries: user-local, team-shared, org-global.
  - All random behavior must be reproducible through seeded generators.
  - Keep business logic in services, not in API route handlers or UI components.
-->

## Implementation Strategy

- Prefer vertical slices over broad framework work.
- {{IMPLEMENTATION_APPROACH}}
<!-- Example:
  - "Build features API-first: schema → service → API → UI."
  - "Prefer locally reproducible authoritative path before multiplayer."
  - "Mobile-first responsive, then desktop enhancement."
-->
- Typical task size should usually fit one of these:
  - 1-3 source files
  - 1 schema/model change
  - 1 UI screen or component
  - 1 service or API endpoint
  - 1 data migration
- A task is not done until code path, data hookup, and minimal verification all exist.

## Agent Guardrails

- Do not bypass shared systems with one-off logic if the docs define a common pipeline.
- {{SHARED_SYSTEM_RULES}}
<!-- Example:
  - "Keep card content data-driven. Do not create one script per card effect."
  - "All API endpoints must use the shared auth middleware."
  - "Do not scatter ad-hoc database queries outside the repository layer."
-->
- If documents, chat notes, and actual files disagree, do not guess. Verify the live files, then update stale guidance.
- Before relying on a file name mentioned in docs or logs, confirm it exists.

## Testing And Verification Expectations

- After production code changes, run the narrowest useful verification step that matches the change.
- {{VERIFICATION_TOOLS}}
<!-- Example (Unity):
  - Prefer Unity MCP: refresh_unity → read_console → run_tests → get_test_job
  Example (Web):
  - Run: `npm run typecheck` → `npm run test -- --related` → `npm run lint`
  Example (Mobile):
  - Run: `npx tsc --noEmit` → `npx jest --changedSince=HEAD~1`
-->
- If full validation is blocked by unrelated issues, still run the narrowest local check you can and document the blocker clearly.

## Research File Rules

Each major source folder contains a `<FolderName>-research.md` file that acts as the living technical reference for that folder.

Whenever you add, remove, or modify source files in a folder, you must update the corresponding research file in the same task.

Research updates should capture:
- New files added and their purpose
- Removed files and why they were removed
- Behavior changes to existing files
- New gaps introduced
- Gaps resolved

Do not defer research file updates to a follow-up task.

## Document Update Rules

{{DOCUMENT_RULES}}
<!-- Example:
  - "Preserve the metadata block in every Document/*.md file."
  - "Save documents as UTF-8 with BOM."
  - "When changing a canonical number or term, update every affected document in the same pass."
  Or simply:
  - "Keep terminology consistent across docs, code comments, and test names."
-->

## Conversation Logging

- Record concise work-session summaries in `{{CHAT_LOG_PATH}}` (e.g., `docs/chat/`, `Document/chat/`).
- Format: `YYYY-MM-DD-HHMM-topic.md`
- Capture: request, decisions, changed files, blockers, next steps. No raw transcripts.

---

# Part 2: Claude Code Contract (Standalone Mode)

**This section is for Claude Code only. Codex must not apply these instructions to itself.**
**Codex: stop reading here. Your instructions are in AGENTS.md.**

When Codex delegates via CLI and instructs you to also read `CLAUDE-HARNESS.md`, that file's harness rules (bounded scope, Generator/Evaluator roles, structured handoff) take priority over the standalone stance below.

---

## Project-specific guardrails

{{CLAUDE_GUARDRAILS}}
<!-- Repeat the most critical 5-7 rules that Claude Code must never violate.
  These should be the subset of Part 1 rules most likely to be broken by AI.
  Example:
  - "Do not put business logic in API route handlers."
  - "Do not confuse MatchScore with StageScore."
  - "Update folder research files when you modify scripts in that folder."
-->

## How to read the project
- Use the shared document priority from Part 1.
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
