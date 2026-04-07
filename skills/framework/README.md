---
role: framework
scope: global
platforms: [claude, gemini]
---

# 스킬: Spring Boot 3 + Kotlin 프레임워크

## 프레임워크 / 런타임

Spring Boot 3.x + Kotlin + Gradle (Kotlin DSL)

## 핵심 컨벤션

### 의존성 주입
- 생성자 주입 사용. `@Autowired` 필드 주입 금지.
- Kotlin에서는 `@Component`, `@Service`, `@Repository` + 생성자 파라미터로 자동 주입.

```kotlin
// 올바른 예
@Service
class UserService(
    private val userRepository: UserRepository
)

// 금지
@Service
class UserService {
    @Autowired
    lateinit var userRepository: UserRepository
}
```

### 트랜잭션
- `@Transactional`은 Service 메서드에 배치.
- 읽기 전용 쿼리는 `@Transactional(readOnly = true)`.
- Controller에 `@Transactional` 금지.

### DTO / Entity 분리
- Entity는 DB 매핑 전용. API 응답에 직접 사용 금지.
- 요청/응답 DTO는 data class로 별도 정의.
- 변환: 확장 함수 권장.

```kotlin
fun User.toDto() = UserDto(id = this.id, name = this.name)
```

### 예외 처리
- 도메인 예외는 커스텀 클래스로 정의.
- 전역 처리는 `@RestControllerAdvice`.

```kotlin
@RestControllerAdvice
class GlobalExceptionHandler {
    @ExceptionHandler(UserNotFoundException::class)
    fun handleUserNotFound(e: UserNotFoundException): ResponseEntity<ErrorResponse> =
        ResponseEntity.status(HttpStatus.NOT_FOUND).body(ErrorResponse(e.message))
}
```

## 피해야 할 안티패턴

| 안티패턴 | 대안 |
|----------|------|
| 필드 주입 (`@Autowired`) | 생성자 주입 |
| Controller에 비즈니스 로직 | Service로 이동 |
| Entity를 API 응답으로 반환 | DTO 변환 후 반환 |
| `@Transactional` 없는 쓰기 작업 | Service에 명시 |
| JPA 지연 로딩을 트랜잭션 밖에서 사용 | Fetch Join 또는 DTO 조회 |
| `!!` 연산자 남용 | null 안전 처리 |

## 유용한 참고

- [Spring Boot 공식 문서](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Kotlin + Spring 공식 가이드](https://spring.io/guides/tutorials/spring-boot-kotlin/)
