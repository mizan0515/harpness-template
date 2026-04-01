# Claude Code Generator Packet 템플릿

이 파일은 Codex가 Claude Code용 Generator 한정 packet을 작성할 때 참조합니다.
Claude Code에 직접 전달되는 것이 아닙니다.

---

## 기본 CLI 형태

```
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read PROJECT-RULES.md and CLAUDE-HARNESS.md first. [packet]"
```

- effort `low` 기본. `high`는 다중 파일 아키텍처 리팩터링 또는 원인 불명의 디버깅 시에만 사용.
- 새 세션 기본. 즉시 FAIL 후 1개 파일 이하 수정 시에만 continued 사용.
- packet당 1-3개 파일 목표, 최대 5개. 초과 시 분할.
- 핸드오프 컨텍스트: 5-10줄. 전체 파일 내용을 프롬프트에 붙여넣지 않기.
- 승인: 재사용 가능한 접두사 승인 우선.
- Windows EPERM: Claude 버그가 아님, 권한 상승 후 재시도.

---

## 새 세션 packet 구조

```
Read PROJECT-RULES.md and CLAUDE-HARNESS.md first.

## Context (현재 상태 5-10줄)
[Codex가 현재 저장소 상태의 간략한 요약을 작성]
- Current milestone: {{CURRENT_MILESTONE}}
- [작업 관련 세부사항]

## Generator Packet
- Sprint: [이름]
- Goal: [한 문장]
- Required Outcome: [구체적 산출물]
- In Scope: [파일/기능]
- Out of Scope: [명시적 제외 대상]
- Files allowed to modify: [목록]
- Files to avoid: [목록]
- Constraints:
  - [이 작업에 해당하는 PROJECT-RULES.md 가드레일]
  - [이 작업에 해당하는 00-common-supplementary.md 주의사항]
- Domain-specific rules:
  - [이 도메인의 핵심 수치, 규칙, 또는 공식]
- Implementation notes: [구체적 힌트, 관련 파일의 현재 상태]
- Required self-check before handoff:
  1. 변경 파일이 scope 내에 있는지
  2. 리서치 파일 업데이트됨 (관련 *-research.md)
  3. 가장 좁은 범위의 검증 수행됨
  4. 가드레일 위반 없음
- Handoff format:
  - Mode used: Generator
  - Files changed
  - What was directly verified
  - What remains unverified
  - Risks / blockers
  - Suggested next packet
```

---

## Delta Packet (동일 작업 내 짧은 수정 루프 전용)

```
Continue the current session. Do not rerun full repository audit.
Apply the delta packet below:

## Delta
- What to fix: [Evaluator의 구체적 항목]
- Files to touch: [목록]
- What NOT to change: [scope 유지]
- Verify after fix: [가장 좁은 범위의 검증]
```

---

## 제약 조건 발췌 가이드 (Codex 참조용)

packet 작성 시 도메인 관련 제약 조건을 포함:

{{CONSTRAINT_GUIDE}}
<!-- 프로젝트의 도메인을 핵심 제약 조건에 매핑.
  예시:
  **API 작업:**
  - 모든 mutation은 tRPC procedure를 통해
  - 보호된 라우트에 인증 미들웨어
  - 오류 형식은 공유 스키마를 따라야 함

  **UI 작업:**
  - 컴포넌트에서 DB 직접 호출 금지
  - 로딩 및 오류 상태 필수
  - 모바일 우선 반응형

  **데이터 작업:**
  - 스키마 변경 시 마이그레이션 필요
  - 시드 데이터는 테스트 시나리오를 커버해야 함
  - 리포지토리 계층 외부에서 raw SQL 금지
-->
