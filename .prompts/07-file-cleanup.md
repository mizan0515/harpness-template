# Unnecessary File Cleanup — Codex Orchestrator Procedure

This prompt is read and executed by Codex. Codex owns the delete decision.

---

## 1. Inventory (Codex direct or delegate to Claude Code)

- Temp/scratch files and directories
- Chat logs / session logs
- Unused prompt files
- Orphan files at project root
- Research files referencing only deleted source files
- {{CLEANUP_TARGETS}}
  <!-- Project-specific locations. Example:
  - `docs/temp/` — temporary plans
  - `docs/chat/` — session logs
  - `.prompts/` — unused prompt files
  - `scripts/` — one-off migration scripts
  -->

## 2. Deletion Criteria (Codex direct)

Safe to delete:
- Temp plans already reflected in parent documents
- Session logs older than {{LOG_RETENTION}} (e.g., 6 months) with no unique context
- Replaced/merged old prompt files
- Empty files, duplicate files
- One-off scripts that have already been executed

Do NOT delete:
- Temp plans with unreflected decisions
- Logs directly related to current sprint
- Unique debugging records
- Active prompt files

## 3. Execute (Claude Code Generator packet)

```
Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
Delete the following files: [list].
Verify no remaining references to deleted files in code or docs.
Update any research files that referenced deleted files.
[scope, constraints, verification]
```

## 4. Verify and Close (Codex direct)

- Deleted files list
- Retained files with reasons
- Remaining cleanup targets
- Research file updates
- commit/push status and reason
