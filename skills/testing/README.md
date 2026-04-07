---
role: testing
scope: global
platforms: [claude, gemini]
---

# 스킬: 테스트 전략

## 원칙

- TC 커버리지 100% 목표
- 테스트는 목적이 명확해야 한다 — "이 테스트가 왜 존재하는가?"에 답할 수 없으면 작성하지 않는다
- 의미 없는 테스트(단순 getter/setter, 프레임워크 동작 검증)는 작성하지 않는다

## 단위 테스트 기준

테스트할 가치가 있는 것:
- 비즈니스 규칙 (도메인 로직)
- 경계값 및 예외 케이스
- 조건 분기가 있는 로직

테스트하지 않는 것:
- 단순 데이터 전달 (DTO 생성, 단순 매핑)
- 프레임워크가 보장하는 동작 (Spring DI, JPA 기본 CRUD)
- 구현 세부사항 (private 메서드, 내부 상태)

## 테스트 구조

Given / When / Then 패턴으로 명확하게.

```kotlin
@Test
fun `주문 금액이 0 이하이면 예외를 던진다`() {
    // given
    val invalidAmount = -1

    // when & then
    assertThrows<IllegalArgumentException> {
        Order.create(amount = invalidAmount)
    }
}
```

## 네이밍

- 한글 backtick 네이밍으로 의도를 명확하게: `` `조건_상황에서_기대동작` ``
- 테스트명만 읽어도 무엇을 검증하는지 알 수 있어야 한다

## Mock 사용 기준

- Mock은 외부 의존성(DB, 외부 API, 메시징) 격리 목적으로만 사용
- 도메인 로직 테스트에서 Mock 남용 금지 — 도메인은 순수 Kotlin이므로 Mock 없이 테스트 가능해야 함
- Mock이 많다면 설계 문제 신호 (의존성 정리 필요)

## 계층별 테스트 범위

| 계층 | 테스트 대상 | 방식 |
|------|------------|------|
| Domain | 비즈니스 규칙, VO 불변식 | 순수 단위 테스트 (Mock 없음) |
| Application | Use Case 흐름, 트랜잭션 경계 | 단위 테스트 (Repository Mock) |
| Presentation | 요청/응답 변환, 유효성 검사 | MockMvc 또는 WebTestClient |
| Infrastructure | 실제 DB 연동 | 통합 테스트 (TestContainers 권장) |
