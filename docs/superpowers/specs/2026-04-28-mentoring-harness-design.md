# 설계: 멘토링 하네스 (소크라테스 리뷰)

**날짜:** 2026-04-28
**상태:** 승인됨

## 배경

7개월 차 주니어 개발자가 AI와 함께 개발하면서 기본기 성장을 목적으로 하는 하네스.
멘토(대기업 시니어)에게 받을 피드백 수준을 AI가 대신 짚어주되, 답을 주지 않고 스스로 사고하게 유도한다.

- Claude Code → 설계, 브레인스토밍, 아키텍처 결정
- Gemini CLI → 구현, 코드 리뷰
- 양쪽 하네스가 심볼릭 링크로 이 저장소를 공유

## 목적

> 코드를 짜고 커밋하기 전, AI가 소크라테스 방식으로 기본기를 짚어준다.
> 답을 주는 게 아니라 질문으로 스스로 이해하게 만든다.

## 리뷰 방식 — 소크라테스 방식

일반 리뷰 (X):
> "Service에 비즈니스 로직 있으면 안 돼, Controller로 옮겨"

소크라테스 방식 (O):
> "여기 이렇게 짠 이유가 뭐예요?"
> "Service 레이어 책임이 뭔지 알아요?"
> "그럼 이 코드가 거기 있는 게 맞아요?"

## 타이밍

커밋 직전. `/review` 커맨드를 직접 실행.

```
코드 작성 → /review 실행 → AI 질문 → 내가 사고 → 이해 후 수정 → 커밋
```

## 구성 방식 — C (하이브리드)

```
CLAUDE.md     → 소크라테스 방식 지침 + 전체 규칙
skills/       → 기본기 원칙 (AI가 리뷰 시 참고하는 지식창고)
commands/     → /review 커맨드 정의
agents/       → 현행 유지 (범위 외)
MCP           → GitHub 연결 유지
```

## 파일 구조

```
.config/.ai/
├── CLAUDE.md
├── skills/
│   ├── oop/README.md          ← 신규 추가 (OOP 기본기)
│   ├── architecture/README.md ← 유지 (Spring 레이어 원칙)
│   ├── coding-style/README.md ← 유지 (Kotlin 스타일)
│   └── git/README.md          ← 유지 (커밋 컨벤션)
├── commands/
│   └── review.md              ← 신규 추가 (/review 커맨드)
└── agents/                    ← 현행 유지
```

## 파일별 역할

### CLAUDE.md
- 소크라테스 리뷰 방식 명시 ("답 주지 말고 질문으로 유도해")
- skills/ 파일들을 리뷰 시 참고하도록 지시
- Claude/Gemini 공통 적용

### skills/oop/README.md (신규)
- 캡슐화, 단일 책임 원칙, 의존성 역전 등 OOP 핵심 원칙
- 7개월 차 기준으로 가장 자주 놓치는 포인트 중심

### skills/architecture/README.md (유지)
- Spring Boot 3 레이어 구조 (Controller→Service→Repository)
- 의존성 방향 원칙

### skills/coding-style/README.md (유지)
- Kotlin 네이밍, val/var, data class 등 스타일 원칙

### skills/git/README.md (유지)
- 커밋 메시지 컨벤션

### commands/review.md (신규)
- `/review` 실행 시 동작 정의
- 변경된 코드 읽고 → skills/ 참고 → 소크라테스 방식으로 질문 시작

## 삭제 대상

현재 목적과 맞지 않아 제거:
- `skills/framework/README.md`
- `skills/security/README.md`
- `skills/testing/README.md`
- `skills/project/README.md`
- `docs/harness-design.md` (초안, 이 문서로 대체)

## 범위 외

- 자동 훅 (pre-commit hook) — 나중에 필요하면 추가
- 테스트 자동화
- 배포/인프라
