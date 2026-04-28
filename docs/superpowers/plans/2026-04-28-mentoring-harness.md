# 멘토링 하네스 구현 계획

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 커밋 직전 `/review` 실행 시 AI가 소크라테스 방식으로 기본기를 짚어주는 하네스 구축

**Architecture:** CLAUDE.md가 리뷰 방식을 정의하고, skills/가 기본기 지식창고 역할을 하며, commands/review.md가 /review 커맨드 동작을 정의한다. Claude/Gemini 양쪽이 심볼릭 링크로 공유한다.

**Tech Stack:** Markdown, YAML frontmatter, Claude Code custom commands

---

### Task 1: 불필요한 파일 삭제

**Files:**
- Delete: `skills/framework/README.md`
- Delete: `skills/security/README.md`
- Delete: `skills/testing/README.md`
- Delete: `skills/project/README.md`
- Delete: `docs/harness-design.md`

- [ ] **Step 1: 파일 삭제**

```bash
git -C ~/.config/.ai rm skills/framework/README.md skills/security/README.md skills/testing/README.md skills/project/README.md docs/harness-design.md
```

- [ ] **Step 2: 삭제 확인**

```bash
ls ~/.config/.ai/skills/
```

Expected output:
```
architecture  coding-style  git
```

- [ ] **Step 3: 커밋**

```bash
git -C ~/.config/.ai commit -m "chore: 목적에 맞지 않는 skills 파일 제거"
```

---

### Task 2: CLAUDE.md 생성

**Files:**
- Create: `.config/.ai/CLAUDE.md`

- [ ] **Step 1: CLAUDE.md 작성**

파일 내용:

```markdown
---
scope: global
platforms: [claude, gemini]
---

# AI 하네스 — 멘토링 보조

## 나는 누구인가

실무 7개월 차 주니어 개발자. Kotlin + Spring Boot 3로 백엔드 개발 중.
기본기를 다지는 것이 최우선 목표다.

## 리뷰 방식 — 소크라테스 원칙

`/review` 실행 시 반드시 이 방식을 따른다.

**절대 금지:**
- 문제를 바로 알려주는 것
- 수정된 코드를 먼저 보여주는 것
- "이렇게 하면 돼요" 식의 답 제공

**반드시 해야 할 것:**
- "왜 이렇게 짰어요?" 로 시작
- 원칙을 물어보는 질문으로 유도 ("이 레이어의 책임이 뭔지 알아요?")
- 내가 스스로 문제를 발견하게 만들기
- 질문은 한 번에 하나씩

**예시:**
```
나쁜 예: "Service에 비즈니스 로직 있으면 안 돼요, Controller로 옮겨요"
좋은 예: "여기 이렇게 짠 이유가 뭐예요?"
         → 답 들은 후 → "Service 레이어 책임이 뭔지 알아요?"
         → 답 들은 후 → "그럼 이 코드가 여기 있는 게 맞아요?"
```

## 리뷰 시 참고 자료

리뷰할 때 아래 skills를 참고해서 어떤 원칙이 위반됐는지 판단한다.
답을 주는 게 아니라, 어떤 원칙이 관련됐는지 파악해서 질문을 설계한다.

- `skills/oop/README.md` — OOP 기본기 원칙
- `skills/architecture/README.md` — Spring 레이어 아키텍처 원칙
- `skills/coding-style/README.md` — Kotlin 코딩 스타일 원칙
- `skills/git/README.md` — 커밋 컨벤션

## 플랫폼 참고

이 파일은 Claude Code와 Gemini CLI 양쪽에서 심볼릭 링크로 읽힌다.
```

- [ ] **Step 2: 파일 존재 확인**

```bash
ls ~/.config/.ai/CLAUDE.md
```

- [ ] **Step 3: 커밋**

```bash
git -C ~/.config/.ai add CLAUDE.md
git -C ~/.config/.ai commit -m "feat: CLAUDE.md 추가 — 소크라테스 리뷰 방식 정의"
```

---

### Task 3: skills/oop/README.md 생성

**Files:**
- Create: `.config/.ai/skills/oop/README.md`

- [ ] **Step 1: 디렉토리 생성 및 파일 작성**

```bash
mkdir -p ~/.config/.ai/skills/oop
```

파일 내용 (`skills/oop/README.md`):

```markdown
---
role: oop
scope: global
platforms: [claude, gemini]
---

# 스킬: OOP 기본기

## 캡슐화 (Encapsulation)

내부 구현을 숨기고, 외부에는 행동만 노출한다.

**자주 하는 실수:**
- 필드를 `public`으로 열어놓고 외부에서 직접 조작
- getter/setter만 있고 실제 행동 메서드가 없는 클래스 ("데이터 덩어리")

```kotlin
// 나쁜 예
class Order {
    var status: String = "PENDING"  // 외부에서 직접 변경 가능
}

// 좋은 예
class Order(private var status: OrderStatus = OrderStatus.PENDING) {
    fun confirm() {
        check(status == OrderStatus.PENDING) { "이미 처리된 주문입니다" }
        status = OrderStatus.CONFIRMED
    }
    fun isCancellable() = status == OrderStatus.PENDING
}
```

**리뷰 질문 힌트:** "이 필드가 외부에서 직접 바뀌면 어떤 문제가 생길 수 있어요?"

---

## 단일 책임 원칙 (SRP)

클래스는 하나의 이유로만 변경된다.

**자주 하는 실수:**
- 하나의 Service가 여러 도메인 로직을 한꺼번에 처리
- Controller가 비즈니스 로직까지 담당

```kotlin
// 나쁜 예 — UserService가 너무 많은 걸 함
class UserService {
    fun register(request: RegisterRequest) { /* ... */ }
    fun sendWelcomeEmail(user: User) { /* ... */ }  // 이메일은 다른 책임
    fun calculateUserScore(user: User): Int { /* ... */ }  // 점수 계산도 다른 책임
}

// 좋은 예 — 책임 분리
class UserService {
    fun register(request: RegisterRequest): User { /* ... */ }
}
class EmailService {
    fun sendWelcomeEmail(user: User) { /* ... */ }
}
```

**리뷰 질문 힌트:** "이 클래스가 변경될 이유가 몇 가지예요?"

---

## 의존성 역전 원칙 (DIP)

구체 클래스가 아닌 추상(인터페이스)에 의존한다.

**자주 하는 실수:**
- Service가 구체 Repository 클래스를 직접 의존
- 외부 라이브러리를 비즈니스 로직 안에서 직접 사용

```kotlin
// 나쁜 예
class OrderService(
    private val orderRepository: OrderJpaRepository  // 구체 클래스 의존
)

// 좋은 예
class OrderService(
    private val orderRepository: OrderRepository  // 인터페이스 의존
)
```

**리뷰 질문 힌트:** "이 클래스를 테스트하려면 DB가 필요한가요? 왜요?"

---

## 조합 우선 원칙 (Composition over Inheritance)

상속보다 조합을 선호한다. 상속은 강한 결합을 만든다.

**자주 하는 실수:**
- 공통 로직을 만들기 위해 무분별한 상속 사용
- 부모 클래스 변경이 자식 클래스를 깨뜨리는 구조

```kotlin
// 나쁜 예 — 상속으로 공통 로직 공유
abstract class BaseService {
    fun validate() { /* ... */ }
}
class UserService : BaseService()
class OrderService : BaseService()

// 좋은 예 — 조합으로 공통 로직 공유
class Validator {
    fun validate() { /* ... */ }
}
class UserService(private val validator: Validator)
class OrderService(private val validator: Validator)
```

**리뷰 질문 힌트:** "부모 클래스가 바뀌면 이 클래스에 어떤 영향이 생겨요?"

---

## 리뷰 체크리스트

- [ ] 필드가 외부에서 직접 조작 가능한가? (캡슐화)
- [ ] 이 클래스가 변경될 이유가 2가지 이상인가? (SRP)
- [ ] 구체 클래스를 직접 의존하는가? (DIP)
- [ ] 공통 로직을 상속으로 공유하고 있는가? (조합 우선)
```

- [ ] **Step 2: 파일 확인**

```bash
cat ~/.config/.ai/skills/oop/README.md | head -5
```

Expected: frontmatter 시작 출력

- [ ] **Step 3: 커밋**

```bash
git -C ~/.config/.ai add skills/oop/README.md
git -C ~/.config/.ai commit -m "feat: skills/oop 추가 — OOP 기본기 원칙 정의"
```

---

### Task 4: commands/review.md 생성

**Files:**
- Create: `.config/.ai/commands/review.md`

- [ ] **Step 1: 디렉토리 생성**

```bash
mkdir -p ~/.config/.ai/commands
```

- [ ] **Step 2: review.md 작성**

파일 내용 (`commands/review.md`):

```markdown
---
description: 커밋 직전 소크라테스 방식으로 코드 리뷰
platforms: [claude, gemini]
---

# /review — 커밋 전 기본기 리뷰

## 실행 순서

1. `git diff HEAD` 또는 `git diff --staged` 로 변경된 코드를 읽는다
2. 변경 내용을 보고 아래 skills 중 관련된 원칙을 파악한다:
   - `skills/oop/README.md`
   - `skills/architecture/README.md`
   - `skills/coding-style/README.md`
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
```

- [ ] **Step 3: 파일 확인**

```bash
ls ~/.config/.ai/commands/
```

Expected: `review.md`

- [ ] **Step 4: 커밋**

```bash
git -C ~/.config/.ai add commands/review.md
git -C ~/.config/.ai commit -m "feat: commands/review 추가 — 소크라테스 리뷰 커맨드 정의"
```

---

### Task 5: 동작 검증

**Files:**
- Read: `.config/.ai/CLAUDE.md`
- Read: `.config/.ai/commands/review.md`

- [ ] **Step 1: 전체 구조 확인**

```bash
find ~/.config/.ai -type f -name "*.md" | grep -v ".git" | grep -v "docs" | sort
```

Expected output:
```
~/.config/.ai/CLAUDE.md
~/.config/.ai/agents/backend-dev.md
~/.config/.ai/agents/designer.md
~/.config/.ai/agents/reviewer.md
~/.config/.ai/commands/review.md
~/.config/.ai/skills/architecture/README.md
~/.config/.ai/skills/coding-style/README.md
~/.config/.ai/skills/git/README.md
~/.config/.ai/skills/oop/README.md
```

- [ ] **Step 2: 삭제된 파일 없는지 확인**

```bash
ls ~/.config/.ai/skills/
```

Expected: `architecture  coding-style  git  oop` (framework, security, testing, project 없어야 함)

- [ ] **Step 3: /review 수동 테스트**

Claude Code 세션에서 간단한 Kotlin 코드 작성 후 `/review` 실행.
AI가 소크라테스 방식으로 질문하는지 확인.

Expected behavior:
- 코드 문제를 바로 알려주지 않음
- "왜 이렇게 짰어요?" 형태의 질문으로 시작
- 한 번에 하나씩 질문

- [ ] **Step 4: 최종 커밋**

```bash
git -C ~/.config/.ai log --oneline
```

4개의 새 커밋이 보여야 함.
