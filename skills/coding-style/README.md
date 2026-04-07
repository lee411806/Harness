---
role: coding-style
scope: global
platforms: [claude, gemini]
---

# 스킬: Kotlin 코딩 스타일 & 설계 원칙

## 설계 원칙

### SOLID
- **S** 단일 책임: 클래스는 하나의 이유로만 변경된다
- **O** 개방/폐쇄: 확장에는 열려 있고, 수정에는 닫혀 있다
- **L** 리스코프 치환: 하위 타입은 상위 타입을 대체할 수 있어야 한다
- **I** 인터페이스 분리: 사용하지 않는 메서드에 의존하지 않는다
- **D** 의존성 역전: 구체가 아닌 추상에 의존한다

### KISS / DRY / YAGNI
- **KISS**: 단순하게. 복잡한 구조보다 읽기 쉬운 코드.
- **DRY**: 중복 금지. 같은 로직이 2곳 이상이면 추상화.
- **YAGNI**: 지금 필요한 것만. 미래를 위한 코드는 작성하지 않는다.

### OOP
- 데이터와 행동을 객체 안에 캡슐화
- 상속보다 조합(Composition) 선호
- 인터페이스로 다형성 확보

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
