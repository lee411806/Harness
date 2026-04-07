# .ai 설정 저장소 내용 채우기 구현 계획

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**목표:** `.ai` 설정 저장소의 5개 템플릿 파일을 Kotlin + Spring Boot 3 기반 개인 사이드 프로젝트에 맞게 채운다.

**아키텍처:** 모든 파일은 단일 소스 마크다운. YAML 프론트매터로 Claude/Gemini 양쪽 하네스에서 읽힌다. Claude는 설계용, Gemini는 구현·리뷰용으로 역할이 분리되어 있다.

**기술 스택:** Kotlin, Spring Boot 3, Gradle, JPA/Hibernate

---

## 파일 목록

| 작업 | 파일 경로 |
|------|----------|
| 수정 | `agents/backend-dev.md` |
| 수정 | `agents/reviewer.md` |
| 수정 | `skills/coding-style/README.md` |
| 수정 | `skills/architecture/README.md` |
| 수정 | `skills/framework/README.md` |

---

### Task 1: agents/backend-dev.md 보강

**파일:**
- 수정: `agents/backend-dev.md`

- [ ] **Step 1: 파일 전체를 아래 내용으로 교체**

```markdown
---
role: backend-dev
scope: global
platforms: [claude, gemini]
---

# 에이전트: 백엔드 개발자

## 역할
Kotlin + Spring Boot 3 기반 서버 사이드 로직, API, 데이터 모델링.

## 레이어 책임

| 레이어 | 책임 | 금지 사항 |
|--------|------|----------|
| Controller | HTTP 요청/응답, 입력 유효성 검사 | 비즈니스 로직 금지 |
| Service | 비즈니스 로직, 트랜잭션 경계 | 직접 HTTP 처리 금지 |
| Repository | 데이터 접근, 쿼리 | 비즈니스 로직 금지 |

의존성 방향: Controller → Service → Repository (역방향 금지)

## Kotlin 관용구

- `var`보다 `val` 선호 (불변성 기본)
- DTO는 `data class`로 정의
- null 반환 대신 `Optional` 또는 예외 사용
- `let`, `also`, `apply`, `run` 적재적소에 활용
- 확장 함수로 유틸리티 분리 (클래스 오염 방지)

## 오류 처리

- 도메인 예외는 sealed class 또는 커스텀 Exception으로 정의
- 전역 처리는 `@RestControllerAdvice` + `@ExceptionHandler`
- 클라이언트에 스택 트레이스 노출 금지

## 출력 형식

- 아키텍처 결정 시 레이어 간 데이터 흐름 명시
- 해피 패스와 오류 케이스를 함께 제시
- 성능·보안 트레이드오프는 명시적으로 언급
```

- [ ] **Step 2: 저장 확인**

파일을 열어 프론트매터와 한글 내용이 정상 출력되는지 확인.

- [ ] **Step 3: 커밋**

```bash
git add agents/backend-dev.md
git commit -m "feat: backend-dev 에이전트 Kotlin/Spring 특화 내용으로 보강"
```

---

### Task 2: agents/reviewer.md 보강

**파일:**
- 수정: `agents/reviewer.md`

- [ ] **Step 1: 파일 전체를 아래 내용으로 교체**

```markdown
---
role: reviewer
scope: global
platforms: [claude, gemini]
primary-consumer: gemini
---

# 에이전트: 코드 리뷰어

## 역할
Kotlin + Spring Boot 3 코드를 정확성, 유지보수성, 팀 기준으로 리뷰.
주 소비자: Gemini CLI

## 리뷰 형식

- **Blocking**: 머지 전 반드시 수정 (버그, 보안 취약점, 레이어 위반)
- **Suggestion**: 개선 가치 있음 (성능, 가독성)
- **Nit**: 사소한 스타일·선호 차이

항상 문제 라인 인용 → 이유 설명 → 수정 예시 제시.
좋은 패턴도 언급할 것 — 지적만 하지 않는다.

## Kotlin 특화 체크리스트

- [ ] `var` 사용이 불가피한 경우인가? (`val`로 대체 가능한지 확인)
- [ ] null 처리가 안전한가? (`!!` 연산자 남용 여부)
- [ ] data class를 DTO에 올바르게 사용했는가?
- [ ] 확장 함수가 적절한 위치에 있는가? (전역 오염 여부)
- [ ] `when` 표현식에 else 분기가 누락되지 않았는가?

## Spring 특화 체크리스트

- [ ] 필드 주입(`@Autowired`) 대신 생성자 주입 사용했는가?
- [ ] `@Transactional`이 Service 레이어에 올바르게 배치되었는가?
- [ ] Controller에 비즈니스 로직이 없는가?
- [ ] Entity와 DTO가 분리되어 있는가?
- [ ] N+1 쿼리 가능성이 있는가?
```

- [ ] **Step 2: 저장 확인**

파일을 열어 프론트매터와 체크리스트가 정상 출력되는지 확인.

- [ ] **Step 3: 커밋**

```bash
git add agents/reviewer.md
git commit -m "feat: reviewer 에이전트 Gemini 주 소비자 기준으로 조정, Kotlin/Spring 체크리스트 추가"
```

---

### Task 3: skills/coding-style/README.md 보강

**파일:**
- 수정: `skills/coding-style/README.md`

- [ ] **Step 1: 파일 전체를 아래 내용으로 교체**

```markdown
---
role: coding-style
scope: global
platforms: [claude, gemini]
---

# 스킬: Kotlin 코딩 스타일

## 네이밍

| 대상 | 규칙 | 예시 |
|------|------|------|
| 변수·함수 | camelCase | `userId`, `findByEmail()` |
| 클래스·인터페이스 | PascalCase | `UserService`, `OrderRepository` |
| 상수 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 파일명 | PascalCase (클래스명과 동일) | `UserService.kt` |
| 패키지 | lowercase, 점 구분 | `com.example.user` |

## 불변성

- 기본은 `val`. `var`는 불가피한 경우에만.
- 컬렉션은 `listOf`, `mapOf` 등 불변 생성자 우선.
- data class 필드도 `val`로 선언.

## 클래스 구조

- 파일당 클래스 하나 (최상위 확장 함수 제외).
- 클래스 내부 순서: 프로퍼티 → init → 생성자 → 공개 메서드 → 비공개 메서드.

## DTO

- DTO는 반드시 `data class`로 정의.
- Entity를 Controller 응답으로 직접 반환하지 않는다.
- 변환 로직은 확장 함수 또는 별도 Mapper 클래스로 분리.

## null 처리

- 경계(Controller 입력, 외부 API 응답)에서는 명시적으로 null 처리.
- `!!` 연산자는 절대 금지 (단, 테스트 코드 제외).
- null 반환 대신 빈 컬렉션, 예외, `Optional` 사용.

## 주석

- *왜*를 설명, *무엇*은 코드가 말한다.
- 주석 처리된 코드는 커밋 전 삭제.
- TODO는 작성자 명시: `// TODO(홍길동): 설명`
```

- [ ] **Step 2: 저장 확인**

파일을 열어 표와 코드 블록이 정상 렌더링되는지 확인.

- [ ] **Step 3: 커밋**

```bash
git add skills/coding-style/README.md
git commit -m "feat: coding-style 스킬 Kotlin 컨벤션으로 교체"
```

---

### Task 4: skills/architecture/README.md 보강

**파일:**
- 수정: `skills/architecture/README.md`

- [ ] **Step 1: 파일 전체를 아래 내용으로 교체**

```markdown
---
role: architecture
scope: global
platforms: [claude, gemini]
---

# 스킬: Spring Boot 3 아키텍처

## 레이어 구조

```
Controller → Service → Repository → Database
               ↑
           Domain Model
```

의존성은 항상 안쪽(도메인)으로만 흐른다. 역방향 의존 금지.

## 패키지 구성

레이어별이 아닌 **기능(feature)별** 패키지 구성.

```
com.example.
├── user/
│   ├── UserController.kt
│   ├── UserService.kt
│   ├── UserRepository.kt
│   └── UserDto.kt
├── order/
│   ├── OrderController.kt
│   ├── OrderService.kt
│   └── ...
└── common/
    ├── exception/
    └── config/
```

## 레이어 간 경계 규칙

- Controller ↔ Service: DTO로만 통신 (Entity 노출 금지)
- Service ↔ Repository: Entity 사용
- 레이어 간 인터페이스 정의는 필요할 때만 (과설계 금지)

## 새 모듈 추가 결정 체크리스트

- [ ] 이 변경이 레이어 경계를 넘는가? 넘는다면 이유가 명확한가?
- [ ] 새 추상화가 2곳 이상에서 사용되는가?
- [ ] 기존 패턴으로 처리할 수 없는가?

## 설계 시 참고

- 이 스킬은 Claude Code(설계 단계)에서 주로 사용.
- 구현 전 레이어 경계와 패키지 구조를 먼저 결정할 것.
```

- [ ] **Step 2: 저장 확인**

파일을 열어 다이어그램과 패키지 구조 예시가 정상 출력되는지 확인.

- [ ] **Step 3: 커밋**

```bash
git add skills/architecture/README.md
git commit -m "feat: architecture 스킬 Spring Boot 3 레이어 구조로 구체화"
```

---

### Task 5: skills/framework/README.md 채우기

**파일:**
- 수정: `skills/framework/README.md`

- [ ] **Step 1: 파일 전체를 아래 내용으로 교체**

```markdown
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
```

- [ ] **Step 2: 저장 확인**

파일을 열어 코드 블록과 표가 정상 렌더링되는지 확인.

- [ ] **Step 3: 커밋**

```bash
git add skills/framework/README.md
git commit -m "feat: framework 스킬 Spring Boot 3 + Kotlin 내용으로 채우기"
```
