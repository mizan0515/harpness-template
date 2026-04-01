# Claude Code Evaluator Packet 템플릿

이 파일은 Codex가 Claude Code용 Evaluator 한정 packet을 작성할 때 참조합니다.
Claude Code에 직접 전달되는 것이 아닙니다.

---

## 기본 CLI 형태

```
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read CLAUDE.md and CLAUDE-HARNESS.md first. [packet]"
```

옵션은 10-generator-패킷.md와 동일.

---

## Packet 구조

```
Read CLAUDE.md and CLAUDE-HARNESS.md first.

## Context (현재 상태 5-10줄)
[Codex가 현재 상태의 간략한 요약을 작성]
- Current milestone: {{CURRENT_MILESTONE}}
- Test baseline: {{TEST_BASELINE}} (예: "45 unit tests, all green")
- [평가 관련 세부사항]

## Evaluator Packet
- Sprint being evaluated: [이름]
- Expected behavior: [구체적 기대 결과]
- Definition of Done: [스프린트 계약에서 그대로 인용]
- Required test scenarios:
  1. [시나리오 1]
  2. [시나리오 2]
- Explicit failure conditions:
  - 기존 테스트 실패 → 즉시 FAIL
  - 빌드/컴파일 오류 → 즉시 FAIL
  - [도메인별 조건]
- Domain checklist (04-domain-checklists.md 참조):
  - [이 스프린트 도메인에 해당하는 체크포인트]
- Verification checks:
  - {{VERIFICATION_STEPS}}
    <!-- 예시 (웹): npm run typecheck → npm run test → npm run lint
         예시 (Unity): mcp refresh → read console → run tests → get results
         예시 (모바일): tsc --noEmit → jest --changedSince -->
- Output format:
  - Verdict: PASS 또는 FAIL
  - 직접 검증한 항목
  - 미검증 항목
  - FAIL인 경우:
    1. 재현 단계
    2. 기대 결과
    3. 실제 결과
    4. 심각도 (blocker / major / minor)
    5. 구체적 수정 요청
```

---

## Evaluator 규칙 (CLAUDE-HARNESS.md 자동 적용, packet에서 강화 가능)

- 독립적 리뷰어 자세 — 원래 구현자가 아닌 입장
- 외관보다 실제 동작 우선
- "should work"는 증거가 아님
- 경미한 이슈와 치명적 차단 요소 구분
- 핵심 흐름 차단, 런타임 오류, 누락된 참조, 깨진 입력, 크래시 → 즉시 FAIL

## 도메인별 FAIL 기준 (Codex가 해당 항목 선택)

{{DOMAIN_FAIL_CRITERIA}}
<!-- 도메인별 FAIL 기준 정의. 예시:

  **API FAIL:**
  - 인증이 필요한 보호되지 않은 엔드포인트
  - 서비스가 아닌 라우트 핸들러에서 비즈니스 로직 계산
  - 응답 스키마가 문서화된 계약과 불일치

  **UI FAIL:**
  - 일반 내비게이션에서 도달 불가능한 페이지
  - 유효성 검사 없이 폼 제출
  - 오류 상태 미처리 (무음 실패)

  **데이터 FAIL:**
  - 마이그레이션이 기존 데이터를 파괴
  - 시드 데이터가 제약 조건 위반 유발
  - 스키마 필드가 명세와 불일치
-->
