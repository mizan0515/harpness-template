# Harness Template

A reusable project template for the **Codex + Claude Code harness architecture**.

> **Codex** plans → **Claude Code** executes → **Codex** judges → repeat
> **Claude Code standalone** works as an autonomous partner when used directly.

## What Problem Does This Solve?

When AI agents work on code, three things go wrong:
1. **Quality drift** — the longer the context, the sloppier the output.
2. **Self-evaluation leniency** — the implementer judges its own work as "looks fine."
3. **Runaway scope** — a small fix becomes a full rewrite nobody asked for.

This harness fixes all three by **separating planning, execution, and evaluation into distinct roles with bounded packets**.

## Architecture

```
┌─────────────────────────────────────────┐
│  Codex (Orchestrator + Planner)         │
│  reads: AGENTS.md + CLAUDE.md Part 1    │
│  ├─ analyzes repo state                 │
│  ├─ selects next task                   │
│  ├─ writes sprint contract              │
│  ├─ delegates bounded packets           │
│  └─ judges PASS / FAIL                  │
└────────────┬───────────┬────────────────┘
             │           │
     Generator Packet  Evaluator Packet
             │           │
┌────────────▼───────────▼────────────────┐
│  Claude Code (Generator or Evaluator)   │
│  reads: CLAUDE.md + CLAUDE-HARNESS.md   │
│  ├─ executes ONE bounded packet         │
│  ├─ stays in scope                      │
│  └─ returns structured handoff          │
└─────────────────────────────────────────┘

  When used directly by user (no Codex):
┌─────────────────────────────────────────┐
│  Claude Code (Standalone)               │
│  reads: CLAUDE.md only                  │
│  ├─ autonomous project partner          │
│  ├─ plans, explores, suggests freely    │
│  └─ user is the decision maker          │
└─────────────────────────────────────────┘
```

## Key Design Decisions

| Decision | Why |
|----------|-----|
| **File separation** (`CLAUDE.md` vs `CLAUDE-HARNESS.md`) | Standalone mode never loads harness rules → zero wasted tokens, no role confusion |
| **`low` effort default** | 90%+ of tasks don't need deep reasoning. Saves cost. |
| **Fresh session per packet** | Avoids paying for accumulated context tokens |
| **1-3 files per packet** | Keeps each call small, predictable, reviewable |
| **Prefix CLI approval** | "Approve similar commands going forward" → no repeated approval dialogs |
| **Evaluator skip rules** | Doc/comment/typo fixes don't need a second session → 50% cost savings |
| **Autonomous loop** | PASS → commit → next sprint immediately. No user wait between sprints. |

## File Structure

```
your-project/
├── CLAUDE.md              ← Claude Code auto-loads (shared rules + standalone mode)
├── CLAUDE-HARNESS.md      ← Loaded only when Codex delegates (harness mode)
├── AGENTS.md              ← Codex auto-loads (orchestrator contract)
└── .prompts/              ← Codex prompt pack (.gitignore'd)
    ├── README.md              Overview and combinations
    ├── 00-common-supplementary.md   Project gotchas, AI mistakes, quick reference
    ├── 01-next-task.md              Main autonomous loop
    ├── 02-consistency-audit.md      Doc↔code consistency audit
    ├── 03-full-audit.md             Large-scale full audit
    ├── 04-domain-checklists.md      Per-domain QA checklists
    ├── 05-bug-fix.md                Bug trace and fix
    ├── 06-data-schema-validation.md Data/schema validation
    ├── 07-file-cleanup.md           File cleanup
    ├── 10-generator-packet.md       Generator packet template
    ├── 11-evaluator-packet.md       Evaluator packet template
    └── 12-session-close.md          Session closure template
```

## Quick Start

### 1. Copy to your project

```bash
# Clone this template
git clone https://github.com/mizan0515/harness-template.git

# Copy files to your project
cp harness-template/CLAUDE.md /path/to/your-project/
cp harness-template/CLAUDE-HARNESS.md /path/to/your-project/
cp harness-template/AGENTS.md /path/to/your-project/
cp -r harness-template/.prompts /path/to/your-project/
```

### 2. Add to `.gitignore`

```
.prompts/
```

### 3. Fill in placeholders

All project-specific content is marked with `{{PLACEHOLDER}}`. Search and replace:

```bash
grep -r '{{' --include="*.md" -l
```

**Must-fill first** (the template won't work without these):

| Placeholder | Where | Example |
|-------------|-------|---------|
| `{{PROJECT_TYPE}}` | CLAUDE.md | "Next.js SaaS dashboard" |
| `{{TECH_STACK}}` | CLAUDE.md | "Next.js 14, tRPC, Prisma, PostgreSQL" |
| `{{CURRENT_MILESTONE}}` | CLAUDE.md, prompts | "MVP launch" |
| `{{REPO_REALITY}}` | CLAUDE.md | 5-15 bullets about what actually exists |
| `{{DOMAIN_RULES}}` | CLAUDE.md | 5-15 invariants agents must not break |
| `{{VERIFICATION_COMMAND}}` | CLAUDE.md | "`npm run typecheck && npm test`" |
| `{{PRIMARY_SPEC}}` | CLAUDE.md, AGENTS.md | "`docs/product-spec.md`" |

### 4. Customize domain checklists

`04-domain-checklists.md` includes commented examples for **web**, **game**, and **mobile** projects. Uncomment what applies, delete the rest, and add your specifics.

### 5. Test it

```bash
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions \
  "Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
   Sprint: test. Goal: Add a comment to README. In Scope: README.md. Out of Scope: everything else."
```

## How The Loop Works

```
01-next-task.md drives this loop:

  ┌→ [Analyze] → [Select task] → [Sprint contract]
  │       ↓
  │  [Generator Packet] → Claude Code implements
  │       ↓
  │  [Review result] → scope check, guardrail check
  │       ↓
  │  [Evaluator Packet] → Claude Code verifies
  │       ↓
  │  PASS → commit/push ─────→ back to top (no user wait)
  │  FAIL → fix packet ──→ Generator again
  └─ FAIL 3x → stop, report to user
```

## Cost Control

| Lever | Default | When to escalate |
|-------|---------|------------------|
| Effort | `low` | `high` for multi-file refactoring or unknown-root-cause debugging only |
| Session | Fresh | Continued only for ≤1 file fix after immediate FAIL |
| Files/packet | 1-3, max 5 | Split into sequential packets if more |
| Handoff context | 5-10 lines | Never paste full file contents |
| Evaluator | Always for logic changes | Skip for doc/comment/typo changes |
| Direct edit | ≤20 lines / ≤2 files | Codex does directly, zero Claude cost |

## Adapting for Your Stack

<details>
<summary><strong>Web (Next.js, Express, Django, Rails, etc.)</strong></summary>

- **Verification**: `npm run typecheck && npm run test && npm run lint`
- **Key domains**: UI, API, Data, Auth, State, Config
- **Key guardrails**: API handlers delegate to services, auth middleware on protected routes, no direct DB from components
</details>

<details>
<summary><strong>Game (Unity, Unreal, Godot, etc.)</strong></summary>

- **Verification**: Unity MCP (`refresh → console → run_tests → get_test_job`)
- **Key domains**: Combat/Gameplay, Network, Map, UI, Data/Schema
- **Key guardrails**: Gameplay rules not in transport/UI code, state ownership boundaries, seed-based determinism
</details>

<details>
<summary><strong>Mobile (React Native, Flutter, Swift, Kotlin)</strong></summary>

- **Verification**: `tsc --noEmit && jest && lint`
- **Key domains**: Offline/Sync, Navigation, Push, Permissions
- **Key guardrails**: Offline-first reads, navigation state preservation, singleton service ownership
</details>

<details>
<summary><strong>Backend / API (Microservices, Serverless)</strong></summary>

- **Verification**: `typecheck && test && lint && openapi-validate`
- **Key domains**: API Contract, Data, Auth, Integration, Config
- **Key guardrails**: Idempotent mutations, schema-first design, no business logic in middleware
</details>

## License

MIT
