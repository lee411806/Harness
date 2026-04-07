---
role: architecture
scope: global
platforms: [claude, gemini]
---

# 스킬: 4계층 아키텍처 (의존성 분리 중심)

## 핵심 원칙

각 계층은 자신의 역할만 수행한다. 의존성은 항상 위 → 아래 방향. 역방향 금지.
계층 간 결합은 추상화(인터페이스)로만 한다.

```
Presentation → Application → Domain ← Infrastructure
```

Infrastructure가 Domain을 구현(implement)하고, Domain은 Infrastructure를 모른다.

## 계층 정의

### Presentation Layer (표현 계층)
- **역할:** 외부 요청/응답 처리, DTO 변환
- **구성:** Controller, DTO, Request/Response 객체
- **규칙:** 비즈니스 로직 금지. Use Case 호출만.

### Application Layer (응용 계층)
- **역할:** 비즈니스 흐름 제어, 트랜잭션 경계
- **구성:** AppService, Facade, Port 인터페이스 정의
- **규칙:** 도메인 규칙을 직접 구현하지 않음. 도메인 객체를 조율하는 역할.

### Domain Layer (도메인 계층)
- **역할:** 핵심 비즈니스 규칙, 데이터 모델
- **구성:** Entity, Value Object(VO), Domain Service
- **규칙:** 외부 프레임워크(Spring, JPA 등) import 금지. 순수 Kotlin.

### Infrastructure Layer (인프라 계층)
- **역할:** 기술적 구현체 (DB, 외부 API, 메시징 등)
- **구성:** Repository 구현체, DB Driver, API Client, Mail Service
- **규칙:** Domain이 정의한 인터페이스를 구현. 도메인 규칙 포함 금지.

## 의존성 분리 패턴

Domain에서 인터페이스 정의 → Infrastructure에서 구현.

```kotlin
// Domain Layer — 인터페이스 정의 (순수 Kotlin)
interface UserRepository {
    fun findById(id: UserId): User?
    fun save(user: User): User
}

// Infrastructure Layer — 구현체
@Repository
class UserRepositoryImpl(
    private val jpaRepo: UserJpaRepo
) : UserRepository {
    override fun findById(id: UserId) =
        jpaRepo.findById(id.value)?.toDomain()

    override fun save(user: User) =
        jpaRepo.save(user.toEntity()).toDomain()
}
```

## 패키지 구성

기능(feature)별로 묶고, 각 기능 안에서 계층 분리.

```
com.example.
├── user/
│   ├── presentation/
│   │   ├── UserController.kt
│   │   └── UserDto.kt
│   ├── application/
│   │   └── UserService.kt
│   ├── domain/
│   │   ├── User.kt
│   │   ├── UserId.kt
│   │   └── UserRepository.kt     ← 인터페이스
│   └── infrastructure/
│       └── UserRepositoryImpl.kt ← 구현체
└── common/
    ├── exception/
    └── config/
```

## 설계 체크리스트

- [ ] 각 계층이 자신의 역할만 수행하는가?
- [ ] Domain이 Spring, JPA 등 외부를 import하지 않는가?
- [ ] 계층 간 통신이 인터페이스(추상화)를 통하는가?
- [ ] Controller에 비즈니스 로직이 없는가?
- [ ] Application Layer가 도메인 규칙을 직접 구현하지 않는가?
- [ ] 새 외부 연동 추가 시 Domain 코드를 건드리지 않아도 되는가?

## 설계 시 참고

- 이 스킬은 Claude Code(설계 단계)에서 주로 사용.
- 구현 전 인터페이스와 의존성 방향을 먼저 확정할 것.
