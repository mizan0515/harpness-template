# Harness Template Setup Guide

## What This Is

A reusable project template for the **Codex + Claude Code harness architecture**:
- **Codex** = Orchestrator + Planner (plans sprints, delegates, judges)
- **Claude Code** = Generator or Evaluator (executes bounded packets)
- **Claude Code standalone** = Autonomous partner (when user interacts directly)

## File Structure

```
your-project/
├── CLAUDE.md              ← Claude Code auto-loads. Part 1 (shared rules) + Part 2 (standalone mode)
├── CLAUDE-HARNESS.md      ← Codex tells Claude Code to read this. Harness rules (bounded mode)
├── AGENTS.md              ← Codex auto-loads. Orchestrator contract
├── .gitignore             ← Add ".prompts/" here
└── .prompts/              ← Codex prompt pack (not committed)
    ├── README.md
    ├── 00-common-supplementary.md   ← Project gotchas, AI mistakes, quick reference
    ├── 01-next-task.md              ← Main autonomous loop
    ├── 02-consistency-audit.md      ← Doc↔code audit
    ├── 03-full-audit.md             ← Large-scale audit
    ├── 04-domain-checklists.md      ← Per-domain QA checklists
    ├── 05-bug-fix.md                ← Bug trace and fix
    ├── 06-data-schema-validation.md ← Data/schema audit
    ├── 07-file-cleanup.md           ← Housekeeping
    ├── 10-generator-packet.md       ← Generator template
    ├── 11-evaluator-packet.md       ← Evaluator template
    └── 12-session-close.md          ← Session closure template
```

## Setup Steps

### 1. Copy files to your project root

```bash
cp CLAUDE.md CLAUDE-HARNESS.md AGENTS.md /path/to/your-project/
cp -r .prompts/ /path/to/your-project/.prompts/
```

### 2. Add `.prompts/` to `.gitignore`

```
# In .gitignore
.prompts/
```

### 3. Fill in `{{PLACEHOLDER}}` sections

Search for `{{` across all files. Each placeholder has a comment explaining what to fill in.

**Priority order** (fill these first):

| File | Placeholders | Priority |
|------|-------------|----------|
| `CLAUDE.md` | `{{PROJECT_TYPE}}`, `{{TECH_STACK}}`, `{{CURRENT_MILESTONE}}`, `{{REPO_REALITY}}`, `{{DOMAIN_RULES}}` | Must-fill |
| `AGENTS.md` | `{{PRIMARY_SPEC}}`, `{{CODEX_REMINDERS}}` | Must-fill |
| `.prompts/00` | `{{REPO_GOTCHAS}}`, `{{AI_MISTAKES}}`, `{{QUICK_REFERENCE}}` | Should-fill |
| `.prompts/01` | `{{MILESTONE_CONTEXT}}` | Should-fill |
| `.prompts/04` | Domain checklists (uncomment relevant, delete irrelevant) | Should-fill |
| Others | Various | Fill as needed |

### 4. Delete unused domain examples

`04-domain-checklists.md` contains examples for web, game, and mobile.
Keep only what applies to your project and fill in project-specific items.

### 5. Create research files

For each major source directory, create `<FolderName>-research.md`:
```
src/components/components-research.md
src/lib/lib-research.md
src/server/server-research.md
```

### 6. Verify the loop works

Test the harness with a trivial task:
```bash
# Codex sends a Generator packet
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions \
  "Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
   Sprint: test-harness. Goal: Add a comment to README.md. In Scope: README.md. Out of Scope: everything else."
```

If Claude Code:
- Reads both files ✅
- Stays in scope ✅
- Produces structured handoff ✅
→ Harness is working.

---

## How It Works

### User directly opens Claude Code
```
User → Claude Code
       reads CLAUDE.md automatically
       Part 1 (shared rules) + Part 2 (standalone mode)
       CLAUDE-HARNESS.md NOT loaded
       → Autonomous partner, can plan and explore freely
```

### Codex delegates to Claude Code
```
Codex → CLI: "Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet..."
        Claude Code reads both files
        CLAUDE.md Part 2 standalone stance overridden by CLAUDE-HARNESS.md
        → Bounded execution, structured handoff, no scope creep
```

### Codex autonomous loop (01-next-task.md)
```
Codex: analyze → select task → write contract
  → Generator packet → review
  → Evaluator packet → verdict
  → PASS: commit, next task (no user wait)
  → FAIL: fix packet, retry (no user wait)
  → FAIL 3x: stop, report to user
```

## Cost Control Summary

| Lever | Default | Escalation |
|-------|---------|------------|
| Effort | `low` | `high` for multi-file refactoring or unknown debugging only |
| Session | fresh | continued for ≤1 file fix after immediate FAIL only |
| Files/packet | 1-3, max 5 | Split into multiple packets if more |
| Context | 5-10 lines | Never paste full file contents |
| Evaluator | Always for logic changes | Skip for doc/comment/typo changes |
| Direct edit | ≤20 lines / ≤2 files | Codex does directly, zero Claude cost |
| Full audit | Targeted 1-2 domains | Full 8-domain only when explicitly needed |

## Adapting for Your Project Type

### Web (Next.js, Express, Django, etc.)
- Verification: `npm run typecheck && npm run test && npm run lint`
- Domain checklists: UI, API, Data, Auth, State, Config
- Key guardrails: API route handlers delegate to services, auth middleware on protected routes

### Game (Unity, Unreal, Godot, etc.)
- Verification: Unity MCP (`refresh → console → run_tests → get_test_job`)
- Domain checklists: Combat, Network, Map, UI, Data/Schema
- Key guardrails: Gameplay rules not in transport/UI, state ownership boundaries

### Mobile (React Native, Flutter, Swift, Kotlin)
- Verification: `tsc --noEmit && jest && lint`
- Domain checklists: Offline/Sync, Navigation, Push, Permissions
- Key guardrails: Offline-first reads, navigation state preservation

### Backend/API (Microservices, serverless)
- Verification: `typecheck && test && lint && openapi-validate`
- Domain checklists: API contract, Data, Auth, Integration, Config
- Key guardrails: Idempotent mutations, schema-first API design
