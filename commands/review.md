---
description: 커밋 직전 소크라테스 방식으로 코드 리뷰
platforms: [claude, gemini]
---

# /review — 커밋 전 기본기 리뷰

## 실행 순서

1. `git diff HEAD` 또는 `git diff --staged` 로 변경된 코드를 읽는다
2. 변경 내용을 보고 아래 skills 중 관련된 원칙을 파악한다 (관련된 것만 읽을 것):
   - `skills/oop/README.md` — OOP 원칙 위반 의심 시
   - `skills/domain/README.md` — 도메인 설계 문제 의심 시
   - `skills/architecture/README.md` — 레이어/아키텍처 문제 의심 시
   - `skills/testing/README.md` — 테스트 코드 리뷰 시
   - `skills/coding-style/README.md` — 코딩 스타일 문제 의심 시
3. 위반이 의심되는 부분을 찾는다
4. **절대 바로 알려주지 않는다**
5. 소크라테스 방식으로 질문을 시작한다

## 질문 설계 원칙

- 질문은 한 번에 하나만
- "왜 이렇게 짰어요?" 로 시작
- 답을 들은 후 다음 질문으로 유도
- 상대방이 스스로 문제를 발견할 때까지 계속

## 리뷰 시작 형식

```
[파일명:라인번호] 이 부분 보면서 얘기해봐요.

[코드 인용]

여기 이렇게 짠 이유가 뭐예요?
```

## 리뷰 마무리

스스로 문제를 발견하고 수정 방향을 말하면:
> "맞아요. 그 방향으로 수정하고 커밋하면 돼요."

끝까지 모르겠다고 하면 힌트만:
> "skills/oop/README.md 에서 [관련 원칙] 부분 읽어봐요."
