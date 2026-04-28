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
- getter/setter만 있고 실제 행동 메서드가 없는 클래스

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
}
```

**리뷰 질문 힌트:** "이 필드가 외부에서 직접 바뀌면 어떤 문제가 생길 수 있어요?"

---

## 단일 책임 원칙 (SRP)

클래스는 하나의 이유로만 변경된다.

```kotlin
// 나쁜 예 — UserService가 너무 많은 책임
class UserService {
    fun register() { }
    fun sendWelcomeEmail() { }   // 이메일은 다른 책임
    fun calculateScore() { }     // 점수 계산도 다른 책임
}

// 좋은 예
class UserService { fun register() { } }
class EmailService { fun sendWelcomeEmail() { } }
```

**리뷰 질문 힌트:** "이 클래스가 변경될 이유가 몇 가지예요?"

---

## 의존성 역전 원칙 (DIP)

구체 클래스가 아닌 추상(인터페이스)에 의존한다.

```kotlin
// 나쁜 예
class OrderService(
    private val repo: OrderJpaRepository  // 구체 클래스 의존
)

// 좋은 예
class OrderService(
    private val repo: OrderRepository  // 인터페이스 의존
)
```

**리뷰 질문 힌트:** "이 클래스를 테스트하려면 DB가 필요한가요? 왜요?"

---

## 조합 우선 원칙 (Composition over Inheritance)

상속보다 조합을 선호한다.

```kotlin
// 나쁜 예 — 공통 로직을 상속으로 공유
abstract class BaseService { fun validate() { } }
class UserService : BaseService()
class OrderService : BaseService()

// 좋은 예 — 조합으로 공유
class Validator { fun validate() { } }
class UserService(private val validator: Validator)
class OrderService(private val validator: Validator)
```

**리뷰 질문 힌트:** "부모 클래스가 바뀌면 이 클래스에 어떤 영향이 생겨요?"

---

## SOLID 한 줄 요약

- **S** 단일 책임: 클래스는 하나의 이유로만 변경
- **O** 개방/폐쇄: 확장에는 열려 있고, 수정에는 닫혀 있음
- **L** 리스코프 치환: 하위 타입은 상위 타입을 대체할 수 있어야 함
- **I** 인터페이스 분리: 사용하지 않는 메서드에 의존하지 않음
- **D** 의존성 역전: 구체가 아닌 추상에 의존

---

## 리뷰 체크리스트

- [ ] 필드가 외부에서 직접 조작 가능한가? (캡슐화)
- [ ] 클래스가 변경될 이유가 2가지 이상인가? (SRP)
- [ ] 구체 클래스를 직접 의존하는가? (DIP)
- [ ] 공통 로직을 상속으로 공유하는가? (조합 우선)
