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
