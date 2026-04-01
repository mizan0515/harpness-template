# 하네스 템플릿 (Harness Template)

**Codex + Claude Code 하네스 아키텍처**를 위한 범용 프로젝트 템플릿.

> **Codex**가 계획 → **Claude Code**가 실행 → **Codex**가 판정 → 반복
> **Claude Code 독립 사용** 시에는 자율적 프로젝트 파트너로 동작

## 어떤 문제를 해결하나?

AI 에이전트가 코드 작업할 때 세 가지가 잘못됩니다:
1. **품질 저하** — 컨텍스트가 길어질수록 출력이 부정확해짐
2. **자기 평가 관대함** — 구현자가 자기 결과물을 "괜찮아 보인다"고 판단
3. **범위 폭주** — 작은 수정이 아무도 요청하지 않은 전면 재작성으로 확대

이 하네스는 **계획, 실행, 평가를 별도 역할로 분리하고 경계된 패킷(bounded packet)으로 작업**하여 세 가지를 모두 해결합니다.

## 아키텍처

```
┌─────────────────────────────────────────┐
│  Codex (오케스트레이터 + 플래너)           │
│  읽기: AGENTS.md + CLAUDE.md Part 1      │
│  ├─ 저장소 상태 분석                       │
│  ├─ 다음 작업 선택                         │
│  ├─ 스프린트 계약 작성                     │
│  ├─ 경계된 패킷 위임                       │
│  └─ PASS / FAIL 판정                     │
└────────────┬───────────┬────────────────┘
             │           │
     Generator 패킷   Evaluator 패킷
             │           │
┌────────────▼───────────▼────────────────┐
│  Claude Code (Generator 또는 Evaluator)  │
│  읽기: CLAUDE.md + CLAUDE-HARNESS.md     │
│  ├─ 패킷 1개만 실행                       │
│  ├─ scope 내에서만 작업                    │
│  └─ 구조화된 핸드오프 반환                  │
└─────────────────────────────────────────┘

  사용자가 직접 사용할 때 (Codex 없이):
┌─────────────────────────────────────────┐
│  Claude Code (독립 모드)                  │
│  읽기: CLAUDE.md만                        │
│  ├─ 자율적 프로젝트 파트너                  │
│  ├─ 자유로운 계획, 탐색, 제안               │
│  └─ 사용자가 의사결정자                     │
└─────────────────────────────────────────┘
```

## 핵심 설계 결정

| 결정 | 이유 |
|------|------|
| **파일 분리** (`CLAUDE.md` vs `CLAUDE-HARNESS.md`) | 독립 모드에서 하네스 규칙 미로드 → 토큰 낭비 제로, 역할 혼동 없음 |
| **`low` effort 기본** | 90%+ 작업이 깊은 추론 불필요. 비용 절약. |
| **패킷당 새 세션** | 누적 컨텍스트 토큰 비용 차단 |
| **패킷당 1-3 파일** | 각 호출을 작고 예측 가능하고 검토 가능하게 유지 |
| **prefix 승인 우선** | "비슷한 명령 앞으로도 허용" → 반복 승인 대화 제거 |
| **Evaluator 생략 규칙** | 문서/주석/오타 수정에 두 번째 세션 불필요 → 50% 비용 절약 |
| **자율 루프** | PASS → commit → 다음 스프린트 즉시 시작. 사용자 대기 없음. |

## 파일 구조

```
your-project/
├── CLAUDE.md              ← Claude Code 자동 로드 (공통 규칙 + 독립 모드)
├── CLAUDE-HARNESS.md      ← Codex 위임 시만 로드 (하네스 모드)
├── AGENTS.md              ← Codex 자동 로드 (오케스트레이터 계약)
└── .prompts/              ← Codex 프롬프트 팩 (.gitignore 대상)
    ├── README.md              개요 및 조합법
    ├── 00-공통-보충규칙.md       프로젝트 혼동포인트, AI 실수, 빠른참조
    ├── 01-다음작업.md            메인 자율 루프
    ├── 02-정합성-감사.md         문서↔코드 정합성 감사
    ├── 03-전체-감사.md           대형 전체 감사
    ├── 04-도메인-체크리스트.md     도메인별 QA 체크리스트
    ├── 05-버그수정.md            버그 추적 및 수정
    ├── 06-데이터-스키마-검증.md    데이터/스키마 검증
    ├── 07-파일정리.md            파일 정리
    ├── 10-generator-패킷.md     Generator 패킷 템플릿
    ├── 11-evaluator-패킷.md     Evaluator 패킷 템플릿
    └── 12-세션마감.md            세션 마감 템플릿
```

## 빠른 시작

### 1. 프로젝트에 복사

```bash
# 템플릿 클론
git clone https://github.com/mizan0515/harpness-template.git

# 파일 복사
cp harpness-template/CLAUDE.md /path/to/your-project/
cp harpness-template/CLAUDE-HARNESS.md /path/to/your-project/
cp harpness-template/AGENTS.md /path/to/your-project/
cp -r harpness-template/.prompts /path/to/your-project/
```

### 2. `.gitignore`에 추가

```
.prompts/
```

### 3. 플레이스홀더 치환

모든 프로젝트 고유 내용은 `{{PLACEHOLDER}}`로 표시되어 있습니다:

```bash
grep -r '{{' --include="*.md" -l
```

**우선 채울 항목** (이것 없이는 작동 안 함):

| 플레이스홀더 | 위치 | 예시 |
|-------------|------|------|
| `{{PROJECT_TYPE}}` | CLAUDE.md | "Next.js SaaS 대시보드" |
| `{{TECH_STACK}}` | CLAUDE.md | "Next.js 14, tRPC, Prisma, PostgreSQL" |
| `{{CURRENT_MILESTONE}}` | CLAUDE.md, prompts | "MVP 출시" |
| `{{REPO_REALITY}}` | CLAUDE.md | 실제 존재하는 것 5-15줄 |
| `{{DOMAIN_RULES}}` | CLAUDE.md | 에이전트가 깨면 안 되는 불변 규칙 5-15개 |
| `{{VERIFICATION_COMMAND}}` | CLAUDE.md | "`npm run typecheck && npm test`" |
| `{{PRIMARY_SPEC}}` | CLAUDE.md, AGENTS.md | "`docs/product-spec.md`" |

### 4. 도메인 체크리스트 커스터마이즈

`04-도메인-체크리스트.md`에 **웹**, **게임**, **모바일** 예시가 주석으로 포함되어 있습니다.
해당 프로젝트에 맞는 것만 주석 해제하고, 나머지는 삭제한 뒤, 프로젝트 고유 항목을 추가하세요.

### 5. 테스트

```bash
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions \
  "Read CLAUDE.md and CLAUDE-HARNESS.md first. Execute Generator Packet below.
   Sprint: test. Goal: README에 주석 추가. In Scope: README.md. Out of Scope: 나머지 전부."
```

Claude Code가 두 파일 읽고 → scope 내에서만 작업 → 구조화된 핸드오프 반환하면 → 하네스 정상 작동.

## 루프 작동 방식

```
01-다음작업.md가 이 루프를 구동:

  ┌→ [분석] → [작업 선택] → [스프린트 계약]
  │       ↓
  │  [Generator 패킷] → Claude Code 구현
  │       ↓
  │  [결과 검토] → scope 확인, 가드레일 확인
  │       ↓
  │  [Evaluator 패킷] → Claude Code 검증
  │       ↓
  │  PASS → commit/push ─────→ 맨 위로 (사용자 대기 없음)
  │  FAIL → 수정 패킷 ──→ Generator 재실행
  └─ FAIL 3회 → 정지, 사용자에게 보고
```

## 비용 통제

| 레버 | 기본값 | 상향 조건 |
|------|--------|----------|
| Effort | `low` | `high`는 multi-file 아키텍처 리팩토링 또는 원인불명 디버깅만 |
| 세션 | 새 세션 | continued는 직전 FAIL 후 ≤1파일 수정만 |
| 파일/패킷 | 1-3개, 최대 5 | 초과 시 순차 패킷으로 분할 |
| 핸드오프 컨텍스트 | 5-10줄 | 전체 파일 내용 붙여넣기 금지 |
| Evaluator | 로직 변경 시 항상 | 문서/주석/오타 변경은 생략 |
| 직접 편집 | ≤20줄 / ≤2파일 | Codex가 직접 수행, Claude Code 비용 0 |

## 프로젝트 타입별 적용

<details>
<summary><strong>웹 (Next.js, Express, Django, Rails 등)</strong></summary>

- **검증**: `npm run typecheck && npm run test && npm run lint`
- **주요 도메인**: UI, API, 데이터, 인증, 상태, 설정
- **주요 가드레일**: API 핸들러는 서비스에 위임, 보호된 라우트에 인증 미들웨어, 컴포넌트에서 직접 DB 금지
</details>

<details>
<summary><strong>게임 (Unity, Unreal, Godot 등)</strong></summary>

- **검증**: Unity MCP (`refresh → console → run_tests → get_test_job`)
- **주요 도메인**: 전투/게임플레이, 네트워크, 맵, UI, 데이터/스키마
- **주요 가드레일**: 게임플레이 규칙을 transport/UI 코드에 묻지 않기, 상태 소유 경계, 시드 기반 결정론
</details>

<details>
<summary><strong>모바일 (React Native, Flutter, Swift, Kotlin)</strong></summary>

- **검증**: `tsc --noEmit && jest && lint`
- **주요 도메인**: 오프라인/동기화, 내비게이션, 푸시, 권한
- **주요 가드레일**: 오프라인 우선 읽기, 내비게이션 상태 보존, 싱글턴 서비스 소유권
</details>

<details>
<summary><strong>백엔드 / API (마이크로서비스, 서버리스)</strong></summary>

- **검증**: `typecheck && test && lint && openapi-validate`
- **주요 도메인**: API 계약, 데이터, 인증, 연동, 설정
- **주요 가드레일**: 멱등성 보장, 스키마 우선 API 설계, 미들웨어에 비즈니스 로직 금지
</details>

## 브랜치

| 브랜치 | 내용 |
|--------|------|
| `main` | 한국어 버전 (이 문서) |
| `english` | 영어 버전 |

## 라이선스

MIT
