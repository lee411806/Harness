# 설계: .ai 설정 저장소 내용 채우기

**날짜:** 2026-04-07
**상태:** 승인됨

## 배경

개인 사이드 프로젝트용 AI 설정 저장소 (`~/.config/.ai`).
`.claude`와 `.gemini` 하네스 양쪽에 마운트되어 있음.

**역할 분담:**
- Claude Code → 설계, 브레인스토밍, 아키텍처 결정
- Gemini CLI → 구현, 코드 리뷰 (Claude 토큰 절약)

**주요 기술 스택:** Kotlin + Spring Boot 3

## 구조 원칙

모든 파일은 단일 소스 마크다운. 양쪽 하네스가 읽을 수 있도록 YAML 프론트매터 적용.

```
---
role: <에이전트명 또는 스킬명>
scope: global
platforms: [claude, gemini]
---
```

## 파일 계획

### 현행 유지
- `skills/git/README.md` — 스택에 무관한 범용 컨벤션, 내용 충분
- `skills/security/README.md` — OWASP 체크리스트, 어떤 백엔드에도 적용 가능
- `skills/project/README.md` — 프로젝트마다 채워 쓰는 템플릿, 의도적으로 비워둠

### 보강 대상 (5개 파일)

| 파일 | 변경 내용 |
|------|----------|
| `agents/backend-dev.md` | Spring 레이어 패턴(Controller→Service→Repository), Kotlin 관용구 추가 |
| `agents/reviewer.md` | Gemini 주 소비자 기준으로 톤 조정, Kotlin 리뷰 포인트 추가 |
| `skills/coding-style/README.md` | 범용 컨벤션을 Kotlin 스타일로 교체 |
| `skills/architecture/README.md` | 범용 레이어를 Spring Boot 3 구조로 구체화 |
| `skills/framework/README.md` | Spring Boot 3 + Kotlin 컨벤션 및 안티패턴으로 채우기 |

### 현행 유지 (가볍게)
- `agents/designer.md` — 백엔드 위주 프로젝트, 현재 수준 충분

## 파일별 내용 범위

**agents/backend-dev.md**
- 레이어 책임: Controller(HTTP), Service(비즈니스 로직), Repository(데이터 접근)
- Kotlin 관용구: data class, 확장 함수, null 안전성, 필요 시 코루틴
- 오류 처리: sealed class Result 패턴 또는 Spring `@ControllerAdvice`

**agents/reviewer.md**
- 주 소비자: Gemini CLI
- 리뷰 형식: Blocking / Suggestion / Nit 유지
- Kotlin 특화 체크: null 안전성, 불변성 선호, 표준 라이브러리 관용적 사용

**skills/coding-style/README.md**
- Kotlin 네이밍 컨벤션 (camelCase, PascalCase, UPPER_SNAKE_CASE)
- 파일 구조: 파일당 클래스 하나, 파일명 = 클래스명
- `var`보다 `val` 선호, DTO는 data class, 경계에서 null 회피

**skills/architecture/README.md**
- Spring Boot 3 레이어 구조와 의존성 방향
- 패키지 구성: 레이어별이 아닌 기능(feature)별 구성
- 레이어 간 인터페이스 경계

**skills/framework/README.md**
- Spring Boot 3 + Kotlin 특화 내용
- 주요 컨벤션: 생성자 주입, `@Transactional` 위치, DTO/Entity 분리
- 안티패턴: 필드 주입, Controller에 비즈니스 로직, 지연 로딩 함정

## 범위 외
- CI/CD, 배포 설정
- 프론트엔드 (UI 없음)
- 프로젝트별 내용 (`skills/project/README.md`에서 각자 채움)
