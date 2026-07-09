# E2E Test Reference

Use this reference for tests that exercise the user-facing boundary through a real running application.

## Scope

Good E2E targets:

- HTTP route and method.
- Serialization and JSON field contract.
- Security filter participation.
- Controller → service → use case → repository → domain → response flow.

Do not create REST service unit tests just to mirror controller JSON behavior. Prefer E2E for REST API contracts.

## Spring Boot E2E

- Keep E2E tests in the runnable app module because they exercise the assembled application boundary.
- Put E2E tests under `apps/<app>/src/test/java` by default. Do not create a separate E2E module or source set unless the project already requires it.
- Use `@SpringBootTest(useMainMethod = ALWAYS, webEnvironment = RANDOM_PORT)`.
- Inject `@LocalServerPort` and send actual HTTP requests.
- Prepare data through application-supported APIs or JPA `EntityManager.persist()` inside a committed transaction.
- If test setup uses `EntityManager` from the test thread and the HTTP request runs on the server thread, commit setup with `TransactionTemplate` before sending the request.
- Use `@DirtiesContext(classMode = BEFORE_EACH_TEST_METHOD)` only when fixture isolation needs a fresh in-memory schema or context.

## Hard Rules

- Use uppercase `E2E` in file and class names, such as `UserProfileE2ETest`.
- Do not call a real HTTP server test an integration test.
- Do not use `@Sql`, raw SQL, or `JdbcTemplate` fixture setup unless the project already requires that pattern.
- Do not mock controllers or services.
- When asserting JSON response bodies, use `jsonPath`.
- Do not validate response JSON with raw string `contains`.

## JSON Body Assertions

Use `JsonPathExpectationsHelper` or an equivalent Spring JSONPath helper.

```java
jsonPath("$.id").assertValue(response.body(), "user-1");
jsonPath("$.items").assertValue(response.body(), empty());
```
