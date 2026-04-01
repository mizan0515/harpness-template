# .prompts/ — Codex Orchestrator Prompt Pack

## Overview

- All prompts in this folder are **input to Codex (Orchestrator)**.
- Codex reads a prompt, follows the AGENTS.md harness loop, and delegates to Claude Code via bounded packets.
- Claude Code reads `CLAUDE.md` automatically. When delegated, Codex also tells it to read `CLAUDE-HARNESS.md`.
- This folder should be in `.gitignore` (project-internal, not shipped).

## Harness Structure

```
Codex (Orchestrator + Planner)
  ├─ reads: AGENTS.md + CLAUDE.md Part 1 + .prompts/
  ├─ plans: sprint contract
  ├─ delegates: bounded packets to Claude Code
  └─ judges: accept / revise / reject

Claude Code (Generator or Evaluator)
  ├─ reads: CLAUDE.md (standalone) + CLAUDE-HARNESS.md (when delegated)
  ├─ executes: one bounded packet per session
  └─ reports: handoff to Codex
```

## File List

| File | Role | Target |
|------|------|--------|
| 00-common-supplementary.md | CLAUDE.md supplement — delta-only rules | Codex reference |
| 01-next-task.md | Plan and execute next task (main loop) | Codex execution |
| 02-consistency-audit.md | Doc↔code consistency audit and fix | Codex execution |
| 03-full-audit.md | Large-scale full audit | Codex execution |
| 04-domain-checklists.md | Domain-specific audit checklists | Codex reference |
| 05-bug-fix.md | Bug reproduction and fix | Codex execution |
| 06-data-schema-validation.md | Data asset/schema validation | Codex execution |
| 07-file-cleanup.md | File cleanup | Codex execution |
| 10-generator-packet.md | Generator packet template | Codex reference |
| 11-evaluator-packet.md | Evaluator packet template | Codex reference |
| 12-session-close.md | Session closure template | Codex reference |

## Combinations

| Situation | Prompts | Codex Action |
|-----------|---------|-------------|
| Autonomous implementation | 01 | Analyze → plan → Generator → Evaluator loop |
| Consistency fix | 02 + 04 | Audit → classify → Generator fix → Evaluator verify |
| Full audit | 03 + 04 | Inventory → domain Evaluators → fix |
| Bug fix | 05 + 04 related domain | Define → trace → fix → verify |
| Data validation | 06 + 04 data section | Schema/asset comparison → fix → verify |
| File cleanup | 07 | Inventory → judge → delete → verify |

## Claude Code CLI Default

```
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read CLAUDE.md and CLAUDE-HARNESS.md first. Then [packet]"
```

- Default: low effort, fresh session, 1 bounded packet
- Approval: prefer reusable prefix approval
