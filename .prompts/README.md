# .prompts/ — Codex Orchestrator 프롬프트 팩

## 개요

- 이 폴더의 모든 프롬프트는 **Codex (Orchestrator)에 대한 입력**입니다.
- Codex는 프롬프트를 읽고, AGENTS.md 하네스 루프를 따르며, 한정 packet을 통해 Claude Code에 위임합니다.
- Claude Code는 `CLAUDE.md`를 자동으로 읽습니다. 위임 시 Codex는 `CLAUDE-HARNESS.md`도 함께 읽도록 지시합니다.
- 이 폴더는 `.gitignore`에 포함되어야 합니다 (프로젝트 내부용, 배포 대상 아님).

## 하네스 구조

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

## 파일 목록

| 파일 | 역할 | 대상 |
|------|------|------|
| 00-공통-보충규칙.md | CLAUDE.md 보충 — 델타 전용 규칙 | Codex 참조 |
| 01-다음작업.md | 다음 작업 계획 및 실행 (메인 루프) | Codex 실행 |
| 02-정합성-감사.md | 문서↔코드 정합성 감사 및 수정 | Codex 실행 |
| 03-전체-감사.md | 대규모 전체 감사 | Codex 실행 |
| 04-도메인-체크리스트.md | 도메인별 감사 체크리스트 | Codex 참조 |
| 05-버그수정.md | 버그 재현 및 수정 | Codex 실행 |
| 06-데이터-스키마-검증.md | 데이터 에셋/스키마 검증 | Codex 실행 |
| 07-파일정리.md | 파일 정리 | Codex 실행 |
| 10-generator-패킷.md | Generator packet 템플릿 | Codex 참조 |
| 11-evaluator-패킷.md | Evaluator packet 템플릿 | Codex 참조 |
| 12-세션마감.md | 세션 마감 템플릿 | Codex 참조 |

## 조합

| 상황 | 프롬프트 | Codex 행동 |
|------|---------|-----------|
| 자율 구현 | 01 | 분석 → 계획 → Generator → Evaluator 루프 |
| 정합성 수정 | 02 + 04 | 감사 → 분류 → Generator 수정 → Evaluator 검증 |
| 전체 감사 | 03 + 04 | 목록 작성 → 도메인별 Evaluator → 수정 |
| 버그 수정 | 05 + 04 관련 도메인 | 정의 → 추적 → 수정 → 검증 |
| 데이터 검증 | 06 + 04 데이터 섹션 | 스키마/에셋 비교 → 수정 → 검증 |
| 파일 정리 | 07 | 목록 작성 → 판단 → 삭제 → 검증 |

## Claude Code CLI 기본값

```
claude --model claude-opus-4-6 --effort low -p --add-dir . --permission-mode bypassPermissions "Read CLAUDE.md and CLAUDE-HARNESS.md first. Then [packet]"
```

- 기본값: low effort, 새 세션, 한정 packet 1개
- 승인: 재사용 가능한 접두사 승인 우선
