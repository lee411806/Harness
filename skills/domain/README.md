---
role: domain
scope: global
platforms: [claude, gemini]
---

# 스킬: 도메인 설계

## TDA (Tell, Don't Ask)

도메인에게 물어보지 말고 행동하게 하라.

**Service 역할:** 흐름(오케스트레이션)만 담당
**Domain 역할:** if / validation / exception 담당

```kotlin
// 나쁜 예 — Service가 도메인에 물어보고 직접 처리
if (order.status == "PENDING") {
    order.status = "CONFIRMED"  // Service가 상태 조작
}

// 좋은 예 — 도메인에게 행동시킴
order.confirm()  // 도메인이 스스로 검증하고 상태 변경
```

**리뷰 질문 힌트:** "이 if문이 Service에 있는 이유가 뭐예요? 이 판단을 도메인이 직접 할 수 없나요?"

---

## Rich Domain Model

빈약한 도메인 모델(데이터 덩어리)이 아닌, 상태와 행위를 함께 가지는 도메인.

```kotlin
// 빈약한 도메인 모델 (나쁜 예)
data class User(
    val id: Long,
    val email: String,
    val status: String  // 외부에서 직접 바꿈
)

// Rich Domain Model (좋은 예)
class User(
    val id: Long,
    val email: String,
    private var status: UserStatus = UserStatus.ACTIVE
) {
    fun deactivate() {
        check(status == UserStatus.ACTIVE) { "이미 비활성화된 사용자입니다" }
        status = UserStatus.INACTIVE
    }
    fun isActive() = status == UserStatus.ACTIVE
}
```

**리뷰 질문 힌트:** "이 클래스에 행위가 없고 데이터만 있는데, 이 도메인이 스스로 할 수 있는 것이 없나요?"

---

## 정적 팩토리 메서드

생성자 대신 정적 팩토리 메서드로 도메인 규칙을 강제한다.

```kotlin
// 생성자 직접 사용 (나쁜 예)
val user = User(id = 1, email = "test@test.com", age = -1)  // 유효하지 않은 값도 허용

// 정적 팩토리 메서드 (좋은 예)
class User private constructor(
    val id: Long,
    val email: String,
    val age: Int
) {
    companion object {
        fun create(email: String, age: Int): User {
            require(age >= 0) { "나이는 0 이상이어야 합니다" }
            require(email.contains("@")) { "유효한 이메일이 아닙니다" }
            return User(id = 0, email = email, age = age)
        }
    }
}
```

**리뷰 질문 힌트:** "이 객체를 생성할 때 잘못된 값이 들어올 수 있나요? 도메인 규칙을 어디서 강제하고 있나요?"

---

## 예외 처리 위치

**Domain:** 비즈니스 규칙 위반 시 예외 발생
**Service:** 예외를 잡거나 흐름 제어 (직접 던지지 않음)
**Presentation:** 예외를 HTTP 응답으로 변환

```kotlin
// 에러코드 체계
// P001 ~ : Presentation 에러
// A001 ~ : Application 에러
// D001 ~ : Domain 에러
```

**자주 하는 실수:**
- Service에서 비즈니스 예외를 직접 던짐
- Controller에서 도메인 규칙 검증
- Spring 기본 예외(404, 405 등) 처리 누락

**리뷰 질문 힌트:** "이 예외가 발생하는 위치가 맞나요? 비즈니스 규칙 위반인가요, 기술적 에러인가요?"

---

## 도메인 네이밍

- Repository스럽지 않게 (JPA 냄새 제거)
- `port` 네이밍 → 계층 의존성 분리가 명확히 보임
- 도메인 중심 네이밍으로 예측 가능한 구조 유지

**리뷰 체크리스트**

- [ ] Service에 if/validation 로직이 있는가? (TDA 위반)
- [ ] 도메인 객체에 행위가 없고 데이터만 있는가? (빈약한 도메인)
- [ ] 생성자로 직접 객체를 만들고 있는가? (정적 팩토리 미사용)
- [ ] 비즈니스 예외가 도메인이 아닌 곳에서 던져지는가?
