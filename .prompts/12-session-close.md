# Claude Code Session Close Packet Template

This file is referenced by Codex when closing a Claude Code work session.

---

## Packet Structure

```
Read CLAUDE.md and CLAUDE-HARNESS.md first.

## Session Closure
Prepare to close this workstream.

1. Sync checklist:
   - [ ] Research files updated (relevant *-research.md)
   - [ ] Document status labels reflect reality
   - [ ] Chat log written (if significant session)
2. Run narrowest useful verification:
   - {{VERIFICATION_COMMAND}}
3. Git decision:
   - Change is self-contained and safe to stage → commit + push
   - Otherwise → state reason clearly
4. Final report:
   - Mode used: Generator or Evaluator
   - Files changed
   - Verified / unverified
   - Risks / blockers
   - commit/push result or reason skipped
   - Suggested next packet
   - Usage note: model, effort, fresh/continued, limit/reset occurred
```

---

## Codex Follow-up

After receiving Claude Code's final report, Codex:
- Verify report aligns with sprint contract
- Judge commit/push skip reason if applicable
- Issue supplementary packet for missed research updates
- Decide next task or additional fix packet
- Record in sprint memo (01 procedure step 9)
