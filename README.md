# AI 워크플로우 — 멘토링 보조

Claude Code와 Gemini CLI가 심볼릭 링크로 공유하는 설정 저장소.
코드 짜다가 커밋 전에 `/review` 하나로 기본기 점검.

> **현재 단계: 가이드 (AI 워크플로우)**
> AI 동작을 가이드하는 구조. 제어(Permissions, Hooks)는 필요할 때 추가한다.
>
> - 가이드 = "이렇게 행동해" 요청 → 현재
> - 제어 = "이건 못 하게 막아" 강제 → 추후 필요 시

---

## 어떻게 동작하나

```
코드 작성 → /review 입력 → AI 질문 → 내가 사고 → 이해 후 수정 → 커밋
```

AI는 답을 바로 주지 않는다. "왜 이렇게 짰어요?" 식의 질문으로 스스로 문제를 발견하게 유도한다.

---

## 사용법

### 커밋 전 리뷰

코드 작성 후, 커밋하기 전에:

```
/review
```

AI가 변경된 코드를 읽고 기본기 관련 질문을 던진다.

---

## 파일 구조

```
.config/.ai/
├── CLAUDE.md              ← AI 행동 지침 (자동으로 읽힘)
├── commands/
│   └── review.md          ← /review 커맨드 정의
├── skills/
│   ├── oop/README.md      ← OOP 기본기 원칙
│   ├── architecture/      ← Spring 레이어 아키텍처 원칙
│   ├── coding-style/      ← Kotlin 코딩 스타일
│   └── git/               ← 커밋 컨벤션
└── agents/                ← 역할별 AI 행동 정의
    ├── backend-dev.md
    ├── reviewer.md
    └── designer.md
```

---

## 각 파일 역할

| 파일 | 역할 | 언제 읽히나 |
|------|------|------------|
| `CLAUDE.md` | 소크라테스 리뷰 방식 지침 | 세션 시작 시 자동 |
| `commands/review.md` | `/review` 동작 정의 | `/review` 실행 시 |
| `skills/oop/` | OOP 기본기 원칙 | `/review` 시 AI가 참고 |
| `skills/architecture/` | Spring 레이어 원칙 | `/review` 시 AI가 참고 |
| `skills/coding-style/` | Kotlin 스타일 원칙 | `/review` 시 AI가 참고 |
| `skills/git/` | 커밋 컨벤션 | 커밋 관련 작업 시 AI가 참고 |

---

## 플랫폼

| AI | 역할 |
|----|------|
| Claude Code | 설계, 브레인스토밍, 아키텍처 결정 |
| Gemini CLI | 구현, 코드 리뷰 |

둘 다 이 저장소를 심볼릭 링크로 읽으므로 설정을 한 곳에서 관리한다.

---

## 수정하고 싶을 때

- **리뷰 방식 바꾸고 싶다** → `CLAUDE.md` 수정
- **새 원칙 추가하고 싶다** → `skills/` 아래 새 폴더 만들고 `README.md` 작성
- **커맨드 추가하고 싶다** → `commands/` 아래 새 `.md` 파일 작성

## 다음 단계 — 제어 추가 (필요할 때)

지금은 가이드 수준. 아래가 필요해지면 추가한다.

| 제어 요소 | 역할 | 추가 방법 |
|----------|------|----------|
| Permissions | 특정 명령어/파일 접근 차단 | `settings.json`에 deny 규칙 추가 |
| Hooks | AI 행동 전후 자동 개입 | `settings.json`에 hook 설정 |
| CLAUDE.md 가드레일 | "절대 금지" 규칙 강제 | `CLAUDE.md`에 금지 사항 추가 |
