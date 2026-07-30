# E2E Test Reference

Use this reference for tests that exercise the user-facing boundary through a real running application.

## Scope

Good E2E targets:

- HTTP route and method.
- Serialization and JSON field contract.
- Security filter participation.
- Controller -> service -> use case -> repository -> domain -> response flow.

Do not create REST service unit tests just to mirror controller JSON behavior. Prefer E2E for REST API contracts.

## Spring Boot E2E

- Keep E2E tests in the runnable app module because they exercise the assembled application boundary.
- Put E2E tests under `apps/<app>/src/test/kotlin` by default. Do not create a separate E2E module or source set unless the project already requires it.
- Use `@SpringBootTest(useMainMethod = ALWAYS, webEnvironment = RANDOM_PORT)`.
- Inject `@LocalServerPort` and send actual HTTP requests.
- Prepare data through application-supported APIs or JPA `EntityManager.persist()` inside a committed transaction.
- If setup uses `EntityManager` from the test thread and the HTTP request runs on the server thread, commit setup with `TransactionTemplate` before sending the request.
- Use `@DirtiesContext(classMode = BEFORE_EACH_TEST_METHOD)` only when fixture isolation needs a fresh in-memory schema or context.

## Hard Rules

- Use uppercase `E2E` in file and class names, such as `UserProfileE2ETest`.
- Do not call a real HTTP server test an integration test.
- Do not use `@Sql`, raw SQL, or `JdbcTemplate` fixture setup unless the project already requires that pattern.
- Do not mock controllers or services.
- Import ordinary test annotations and assertions from `kotlin.test`, not JUnit.
- When asserting JSON response bodies, use the HTTP client's `jsonPath` support or parse JSON and assert fields with `kotlin.test`.
- Do not validate response JSON with raw string `contains`.

## JSON Body Assertions

Prefer the JSONPath or parsed JSON API already used by the project. Assert the intended field path and value, not incidental serialization text.

```kotlin
jsonPath("$.id").assertValue(response.body, "user-1")
jsonPath("$.items").assertValue(response.body, emptyList<Any>())
```

When the client has no suitable JSON assertion API, deserialize the body and use `assertEquals`, `assertContentEquals`, or `assertIs` from `kotlin.test`.
