---
role: coding-style
scope: global
platforms: [claude, gemini]
---

# 스킬: Kotlin 코딩 스타일

## 절대 금지

```kotlin
// !! 연산자 금지 — NullPointerException 직접 유발
val name = user!!.name  // 절대 금지

// 좋은 예
val name = user?.name ?: throw IllegalStateException("user가 없습니다")
```

---

## 불변성

- 기본은 `val`. `var`는 불가피한 경우에만.
- 컬렉션은 `listOf`, `mapOf` 등 불변 생성자 우선.
- data class 필드도 `val`로 선언.

---

## 네이밍

| 대상 | 규칙 | 예시 |
|------|------|------|
| 변수·함수 | camelCase | `userId`, `findByEmail()` |
| 클래스·인터페이스 | PascalCase | `UserService`, `OrderRepository` |
| 상수 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 파일명 | PascalCase (클래스명과 동일) | `UserService.kt` |

**의도를 명확히:**
```kotlin
// 나쁜 예
fun extractUsername()

// 좋은 예
fun extractUsernameFrom(token: String)
```

---

## DTO

- DTO는 반드시 `data class`로 정의.
- Entity를 Controller 응답으로 직접 반환하지 않는다.
- 변환 로직은 확장 함수 또는 별도 Mapper로 분리.

---

## null 처리

- 경계(Controller 입력, 외부 API 응답)에서 명시적으로 null 처리.
- `!!` 절대 금지 (테스트 코드도 지양).
- null 반환 대신 빈 컬렉션, 예외 사용.

---

## 클래스 구조

- 파일당 클래스 하나.
- 클래스 내부 순서: 프로퍼티 → init → 생성자 → 공개 메서드 → 비공개 메서드.

---

## 린트

- ktlint 적용 — build 전에 자동 실행.
- import 무분별 사용 금지 (필요한 것만).
- 주석 처리된 코드는 커밋 전 삭제.
- 절대경로 커밋 금지.

---

## 주석

- *왜*를 설명, *무엇*은 코드가 말한다.
- TODO는 작성자 명시: `// TODO(이름): 설명`

---

## 리뷰 체크리스트

- [ ] `!!` 연산자를 사용하는가?
- [ ] `var`를 불필요하게 사용하는가?
- [ ] 함수명이 의도를 명확히 드러내는가?
- [ ] Entity를 직접 반환하는가?
- [ ] import가 무분별하게 들어가 있는가?
- [ ] 주석 처리된 코드가 있는가?
