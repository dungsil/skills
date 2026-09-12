# E2E 테스트

실제로 실행한 애플리케이션을 통해 사용자에게 노출되는 경계를 검증할 때 읽는다.

## 적용 범위

HTTP 경로·메서드, 직렬화·JSON 필드 계약, 보안 필터 참여와 컨트롤러 -> 서비스 -> 유스케이스 -> 저장소 -> 도메인 -> 응답 동작을 검증한다.

컨트롤러 JSON 동작을 반복하기 위한 REST 서비스 단위 테스트를 만들지 않는다. REST API 계약에는 E2E를 우선한다.

## Spring Boot E2E

- 조립한 애플리케이션 경계를 검증하므로 실행 앱 모듈에 둔다.
- 기본 경로는 `apps/<app>/src/test/kotlin`이다. 프로젝트가 이미 요구하지 않으면 별도 모듈·소스 세트를 만들지 않는다.
- `@SpringBootTest(useMainMethod = ALWAYS, webEnvironment = RANDOM_PORT)`를 사용한다.
- `@LocalServerPort`를 주입받아 실제 HTTP 요청을 보낸다.
- 앱이 지원하는 API나 커밋된 트랜잭션 안의 `EntityManager.persist()`로 데이터를 준비한다.
- 테스트 스레드에서 준비하고 서버 스레드에서 HTTP를 처리하면 요청 전에 `TransactionTemplate`으로 준비 데이터를 커밋한다.
- 픽스처 격리에 새 인메모리 스키마·컨텍스트가 필요할 때만 `@DirtiesContext(classMode = BEFORE_EACH_TEST_METHOD)`를 사용한다.

## 필수 규칙

- 파일·클래스명에 `UserProfileE2ETest`처럼 대문자 `E2E`를 사용한다.
- 실제 HTTP 서버 테스트를 통합 테스트라고 부르지 않는다.
- 프로젝트가 이미 요구하지 않으면 `@Sql`, 원시 SQL이나 `JdbcTemplate`으로 픽스처를 준비하지 않는다.
- 컨트롤러·서비스를 모킹하지 않는다.
- 일반 테스트 애너테이션·단언은 JUnit 대신 `kotlin.test`에서 가져온다.
- JSON은 HTTP 클라이언트의 `jsonPath`나 파싱한 필드를 `kotlin.test`로 검증한다. 원시 문자열의 `contains`로 검증하지 않는다.

## JSON 본문 단언

프로젝트의 기존 JSONPath·파싱 API를 우선한다. 부수적인 직렬화 문자열보다 의도한 필드 경로와 값을 단언한다.

```kotlin
jsonPath("$.id").assertValue(response.body, "user-1")
jsonPath("$.items").assertValue(response.body, emptyList<Any>())
```

클라이언트에 적절한 API가 없으면 역직렬화하고 `kotlin.test`의 `assertEquals`, `assertContentEquals`, `assertIs`로 검증한다.
