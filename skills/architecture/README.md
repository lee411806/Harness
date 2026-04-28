---
role: architecture
scope: global
platforms: [claude, gemini]
---

# 스킬: 아키텍처 & 설계

## 헥사고날 아키텍처 (포트 & 어댑터)

Domain ↔ Infra 의존성 분리. Domain은 기술(JPA 등)을 몰라야 한다.

```
Presentation → Application → Domain ← Infrastructure
```

- **Port:** Domain이 정의하는 인터페이스
- **Adapter:** Infrastructure가 Port를 구현

```kotlin
// Domain Layer — Port 정의 (순수 Kotlin)
interface UserRepository {
    fun findById(id: UserId): User?
    fun save(user: User): User
}

// Infrastructure Layer — Adapter 구현
@Repository
class UserRepositoryImpl(
    private val jpaRepo: UserJpaRepo
) : UserRepository {
    override fun findById(id: UserId) =
        jpaRepo.findById(id.value)?.toDomain()
}
```

**리뷰 질문 힌트:** "Domain 레이어에서 JPA 어노테이션이 보이는데, Domain이 기술을 알아야 할 이유가 있나요?"

---

## 레이어 책임

| 레이어 | 책임 | 금지 |
|--------|------|------|
| Presentation | HTTP 요청/응답, DTO 변환 | 비즈니스 로직 |
| Application | 흐름 제어, 트랜잭션 경계 | 도메인 규칙 직접 구현 |
| Domain | 핵심 비즈니스 규칙 | Spring/JPA import |
| Infrastructure | 기술 구현체 (DB, 외부 API) | 도메인 규칙 포함 |

---

## UseCase 설계

메서드 단위가 아닌 **행위(시나리오) 단위**로 설계.

```kotlin
// 나쁜 예 — 메서드 단위
interface UserUseCase {
    fun createUser()
    fun validateEmail()
    fun sendWelcomeEmail()
}

// 좋은 예 — 행위 단위
interface RegisterUserUseCase {
    fun register(command: RegisterUserCommand): User
}
```

---

## Command 패턴

Controller → String 직접 받지 말고 Command 객체로 감싸기.

```kotlin
// 나쁜 예 — 파라미터 추가 시 모든 Controller 영향
fun join(email: String, password: String) { }

// 좋은 예 — Command 객체로 감싸서 변경 영향 최소화
data class UserJoinCommand(
    val email: String,
    val password: String
)
fun join(command: UserJoinCommand) { }
```

**리뷰 질문 힌트:** "파라미터가 늘어나면 이 Controller 시그니처를 어떻게 바꿔야 해요?"

---

## 의존성 분리 원칙

- JPA Repository를 직접 쓰지 말고 한 단계 감싸기
- 특정 기술(JPA, MyBatis)에 강결합 금지
- 어댑터 레벨에서 교체 가능해야 함

```kotlin
// 나쁜 예 — JPA 강결합
class OrderService(
    private val orderJpaRepository: OrderJpaRepository  // JPA 직접 의존
)

// 좋은 예 — 추상화 의존
class OrderService(
    private val orderRepository: OrderRepository  // 인터페이스 의존
)
```

---

## 패키지 구성

레이어별이 아닌 **기능(feature)별**로 묶고, 각 기능 안에서 레이어 분리.

```
com.example.
├── user/
│   ├── presentation/
│   ├── application/
│   ├── domain/
│   └── infrastructure/
└── order/
    ├── presentation/
    ├── application/
    ├── domain/
    └── infrastructure/
```

---

## 리뷰 체크리스트

- [ ] Domain이 Spring/JPA를 직접 import하는가?
- [ ] Controller에 비즈니스 로직이 있는가?
- [ ] Service가 도메인 규칙을 직접 구현하는가?
- [ ] JPA Repository를 직접 의존하는가?
- [ ] UseCase가 메서드 단위로 잘게 쪼개져 있는가?
- [ ] 파라미터를 Command 객체 없이 직접 받는가?
