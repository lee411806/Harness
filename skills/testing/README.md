---
role: testing
scope: global
platforms: [claude, gemini]
---

# 스킬: 테스트 전략

## 테스트 계층

| 계층 | 목적 | 범위 |
|------|------|------|
| Unit | 비즈니스 로직이 올바르게 동작하는지 검증 | 클래스 단위 |
| Integration | 컴포넌트 간 협력 관계에서 동작이 올바른지 검증 | 모듈 간 |
| Acceptance | 사용자 시나리오 기반 전체 흐름 검증 | E2E |

> **핵심:** 구현이 아니라 동작을 테스트. 구현은 개발자 관점, 동작은 사용자 관점.

---

## 좋은 테스트 기준

> 리팩토링 시 동작이 바뀌었을 때만 실패해야 한다.

```
실패 = 동작 변경 감지 (정상)
실패 = 구현 방식 변경 감지 (나쁜 테스트)
```

---

## 안티패턴

### 매직 스트링
```kotlin
// 나쁜 예
assertThat(user.status).isEqualTo("ACTIVE")

// 좋은 예
assertThat(user.status).isEqualTo(UserStatus.ACTIVE)
```

### @BeforeEach 남용
각 테스트 실행 전 공통 초기화에 사용하지만, 남용하면 테스트 의도가 숨겨진다.
KOJO/POJO 방식으로 대체 가능.

### Mock을 검증하는 테스트
```kotlin
// 나쁜 예 — 내가 설정한 mock을 내가 검증
given(userRepository.findById(1L)).willReturn(user)
verify(userRepository).findById(1L)  // mock 자체를 검증하는 것

// 좋은 예 — 실제 동작 결과를 검증
val result = userService.getUser(1L)
assertThat(result.email).isEqualTo("test@test.com")
```

---

## Mocking 원칙

- `any()` 적극 활용 — 값 자체가 중요하지 않을 때
- stubbing과 mock 구분
- 내가 만든 mock을 검증하지 말 것
- given 테스트가 되어야 함 (mock을 위한 테스트 X)

```kotlin
// any() 활용
given(userRepository.findById(any())).willReturn(user)
```

---

## 테스트 성능 최적화

**H2 / SQLite 활용**
```kotlin
// test/resources/application-test.yml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
```

**@DataJpaTest**
- 일반 SpringBootTest는 전체 서버를 띄움
- @DataJpaTest는 JPA 관련만 로딩 → 속도 향상

**TestContainer**
- 실제 환경과 유사하지만 비용이 큼
- 빠른 테스트와 신뢰성 사이 균형 필요

---

## Test Object Factory

테스트 전용 객체 생성 책임을 분리.

```kotlin
// 테스트 전용 팩토리
object UserFixture {
    fun create(
        id: Long = 1L,
        email: String = "test@test.com",
        status: UserStatus = UserStatus.ACTIVE
    ) = User.create(email = email, status = status)
}

// 테스트에서 사용
val user = UserFixture.create(email = "custom@test.com")
```

---

## 리뷰 체크리스트

- [ ] 구현이 아닌 동작을 테스트하는가?
- [ ] 매직 스트링을 사용하는가?
- [ ] @BeforeEach를 남용하는가?
- [ ] mock을 검증하는 테스트인가?
- [ ] 리팩토링 시 깨질 가능성이 있는가?
- [ ] 테스트 의도가 명확하게 읽히는가?
