# 하네스 템플릿 설정 가이드

## 이게 뭔가요

**Codex + Claude Code 하네스 아키텍처**를 위한 범용 프로젝트 템플릿입니다:
- **Codex** = 오케스트레이터 + 플래너 (스프린트 계획, 위임, 판정)
- **Claude Code** = Generator 또는 Evaluator (경계된 패킷 실행)
- **Claude Code 독립** = 자율적 파트너 (사용자가 직접 사용 시)

## 파일 구조

```
your-project/
├── PROJECT-RULES.md       ← 공유 프로젝트 규칙 (모든 에이전트 공통)
├── CLAUDE.md              ← Claude Code 자동 로드. 독립 모드 계약
├── CLAUDE-HARNESS.md      ← Codex가 Claude Code에 읽기 지시. 하네스 규칙 (경계 모드)
├── AGENTS.md              ← Codex 자동 로드. 오케스트레이터 계약
├── .gitignore             ← 여기에 ".prompts/" 추가
└── .prompts/              ← Codex 프롬프트 팩 (커밋 안 함)
    ├── README.md
    ├── 00-공통-보충규칙.md
    ├── 01-다음작업.md
    ├── 02-정합성-감사.md
    ├── 03-전체-감사.md
    ├── 04-도메인-체크리스트.md
    ├── 05-버그수정.md
    ├── 06-데이터-스키마-검증.md
    ├── 07-파일정리.md
    ├── 10-generator-패킷.md
    ├── 11-evaluator-패킷.md
    └── 12-세션마감.md
```

## 설정 단계

### 1. 프로젝트 루트에 파일 복사

```bash
cp PROJECT-RULES.md CLAUDE.md CLAUDE-HARNESS.md AGENTS.md /path/to/your-project/
cp -r .prompts/ /path/to/your-project/.prompts/
```

### 2. `.gitignore`에 `.prompts/` 추가

```
.prompts/
```

### 3. `{{PLACEHOLDER}}` 채우기

모든 파일에서 `{{`를 검색하세요. 각 플레이스홀더에 설명 주석이 있습니다.

**우선순위** (이것부터):

| 파일 | 플레이스홀더 | 우선도 |
|------|-------------|--------|
| `PROJECT-RULES.md` | `{{PROJECT_TYPE}}`, `{{TECH_STACK}}`, `{{CURRENT_MILESTONE}}`, `{{REPO_REALITY}}`, `{{DOMAIN_RULES}}` | 필수 |
| `AGENTS.md` | `{{PRIMARY_SPEC}}`, `{{CODEX_REMINDERS}}` | 필수 |
| `.prompts/00` | `{{REPO_GOTCHAS}}`, `{{AI_MISTAKES}}`, `{{QUICK_REFERENCE}}` | 권장 |
| `.prompts/01` | `{{MILESTONE_CONTEXT}}` | 권장 |
| `.prompts/04` | 도메인 체크리스트 (해당만 주석 해제, 나머지 삭제) | 권장 |
| 기타 | 다양 | 필요 시 |

### 4. 불필요한 도메인 예시 삭제

`04-도메인-체크리스트.md`에 웹, 게임, 모바일 예시가 있습니다.
해당 프로젝트에 맞는 것만 남기세요.

### 5. research 파일 생성

주요 소스 디렉토리마다 `<폴더명>-research.md`를 만드세요:
```
src/components/components-research.md
src/lib/lib-research.md
src/server/server-research.md
```

### 6. 루프 동작 확인

```bash
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions \
  "Read PROJECT-RULES.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
   Sprint: test-harness. Goal: README.md에 주석 추가. In Scope: README.md. Out of Scope: 나머지 전부."
```

Claude Code가 두 파일 읽고 → scope 내에서만 작업 → 구조화된 핸드오프 반환하면 → 하네스 정상 작동.

---

## 작동 원리

### 사용자가 직접 Claude Code 열 때
```
사용자 → Claude Code
         CLAUDE.md 자동 로드 → PROJECT-RULES.md 읽기 지시
         CLAUDE-HARNESS.md 로드 안 됨
         → 자율적 파트너, 자유롭게 계획·탐색 가능
```

### Codex가 Claude Code에 위임할 때
```
Codex → CLI: "Read PROJECT-RULES.md and CLAUDE-HARNESS.md first. Execute Generator Packet..."
        Claude Code: CLAUDE.md(자동) + PROJECT-RULES.md + CLAUDE-HARNESS.md 로드
        독립 모드가 CLAUDE-HARNESS.md에 의해 재정의됨
        → 경계된 실행, 구조화된 핸드오프, scope 확장 금지
```

### Codex 자율 루프 (01-다음작업.md)
```
Codex: 분석 → 작업 선택 → 계약 작성
  → Generator 패킷 → 검토
  → Evaluator 패킷 → 판정
  → PASS: commit, 다음 작업 (사용자 대기 없음)
  → FAIL: 수정 패킷, 재시도 (사용자 대기 없음)
  → FAIL 3회: 정지, 사용자에게 보고
```

## 비용 통제 요약

| 레버 | 기본값 | 상향 조건 |
|------|--------|----------|
| Effort | `low` | `high`는 multi-file 리팩토링 또는 원인불명 디버깅만 |
| 세션 | 새 세션 | continued는 직전 FAIL 후 ≤1파일 수정만 |
| 파일/패킷 | 1-3개, 최대 5 | 초과 시 분할 |
| 컨텍스트 | 5-10줄 | 전체 파일 내용 붙여넣기 금지 |
| Evaluator | 로직 변경 시 항상 | 문서/주석/오타 변경은 생략 |
| 직접 편집 | ≤20줄 / ≤2파일 | Codex가 직접, Claude 비용 0 |
| 전체 감사 | 타겟 1-2 도메인 | 전체 도메인은 꼭 필요할 때만 |
