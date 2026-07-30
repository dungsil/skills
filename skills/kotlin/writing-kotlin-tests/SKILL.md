---
name: writing-kotlin-tests
description: Write, improve, or review Kotlin tests with kotlin-test, MockK, Spring Boot, and Spring Data JPA across unit, integration, and E2E levels. Use only for Kotlin test work, including domain, use-case, entity, validation, repository, adapter, coroutine, HTTP API, nullability, or Kotlin test-level selection decisions.
---

# Writing Kotlin Tests

Use this skill to choose the right test level first, then write compact contract-focused Kotlin tests.

Use `kotlin.test` APIs for test annotations and assertions. Use MockK when interaction matters. Do not use JUnit assertion or annotation APIs, Mockito, AssertJ, Hamcrest, Truth, or another assertion library unless an existing framework assertion requires it. For E2E JSON body checks, use `jsonPath` or parse JSON and assert fields with `kotlin.test`; Hamcrest matchers are allowed only inside an existing `jsonPath` assertion API.

## Workflow

1. Read the target code, nearby tests, build configuration, Kotlin version, platform, and source set before writing anything.
2. Decide the lowest useful test level: unit, integration, or E2E.
3. Load the matching reference only when needed:
   - Unit tests: [references/unit.md](references/unit.md)
   - Integration tests: [references/integration.md](references/integration.md)
   - E2E tests: [references/e2e.md](references/e2e.md)
4. Identify the public contract and add the smallest useful test set.
5. Avoid duplicate coverage across levels.
6. Put unit and integration tests in the module and source set that own the behavior.
7. Run the narrowest relevant test command first, then broader tests when risk justifies it.

## Test Levels

Choose the lowest useful level:

- Domain value object, entity helper, use-case branch, validation rule, coroutine transformation, repository adapter call shape: unit test.
- JPA entity mapping, repository query, transaction/lifecycle behavior, Spring bean wiring: integration test.
- HTTP route, serialization, security filter, controller-service-use-case-repository flow: E2E test.

Avoid duplicate coverage:

- If E2E verifies HTTP -> repository -> domain -> JSON, do not also assert every mapped domain field in adapter unit tests.
- Keep adapter unit tests only for adapter-specific policy, such as forcing `activeOnly = true` on repository calls.
- Keep entity unit tests for pure helper contracts such as `isDeleted()` and `setDeleted()`.

## Dependencies

- Use the project's existing `kotlin-test` integration and test runner. Do not change runner adapters merely to replace source imports.
- Import annotations and assertions from `kotlin.test`, such as `Test`, `BeforeTest`, `assertEquals`, `assertContentEquals`, `assertFailsWith`, `assertIs`, and `assertNotNull`.
- Use MockK, not Mockito, for mocks, stubs, spies, or interaction verification.
- Use `kotlinx-coroutines-test` only when coroutine timing, dispatchers, cancellation, or virtual time are part of the contract and the project already provides it or the test requires it.

## Test Fixtures

- Use Gradle `java-test-fixtures` only when fixtures must be shared across modules; Kotlin fixtures belong under `src/testFixtures/kotlin`.
- Prefer fixtures in the adapter module that owns the fixture type.
- App-level E2E or integration tests may depend on `testFixtures(project(":<adapter-module>"))` when they need adapter-owned entity fixtures.
- Do not create a separate fixture module for a single adapter's test data.
- Prefer small factory functions with named arguments and useful defaults over builders that only assign properties.

## Common Structure

Keep tests flat when the class has one meaningful behavior surface. Split a large test class by behavior or public entry point instead of depending on JUnit-specific `@Nested` groups.

Place successful or valid cases first, then boundary cases, then invalid or exception cases.

Use `@BeforeTest` when multiple tests repeat the same setup. Keep shared preconditions in setup and behavior-specific values inside the test function.

## Naming

- Prefer descriptive backtick function names when the repository permits them; otherwise follow its established `should...` convention.
- Korean backtick test names must end with `~해야 한다` or `~이어야 한다`.
- Use `shouldBeRejectedWhen...` for invalid inputs when identifier-style names are required.
- Use `shouldBeReturned...When...` for getter or helper return behavior.
- Use `shouldBeThrownWhen...` for expected exception paths.
- Always write E2E with uppercase `E2E` in class and file names, such as `UserProfileE2ETest`.

## Assertions

- Prefer one action per test and assert only its observable result.
- Use structural equality or several focused `kotlin.test` assertions when one action produces multiple observable properties.
- Use `assertFailsWith<ExpectedException>` for exception contracts.
- Use `assertContentEquals` for arrays and sequences whose ordered contents are the contract.
- Avoid asserting exception messages unless the message is part of the public contract.
- Do not use JUnit assertions through fully qualified calls or aliases.

## Kotlin Nullability

Treat Kotlin types as the source of truth.

- Do not write `null` tests for non-null Kotlin parameters, properties, or return types; such calls are outside the Kotlin contract.
- Cover `null` explicitly for nullable types, including the policy encoded by the production code.
- Do not use `null as T`, reflection, or Java interop only to bypass the compiler unless Java-callable misuse defense is itself the public contract.
- For Java platform types, make nullability explicit at the production boundary before testing internal behavior. Do not spread `!!` into tests.
- Keep test doubles and fixtures at least as strict as the production nullability contract.

## Review Pass

Before finishing:

- Check the chosen test level is the lowest useful level.
- Check level-specific reference rules were followed.
- Check success cases appear before failure cases.
- Check imports use `kotlin.test`, not JUnit test or assertion APIs.
- Check MockK is used instead of Mockito and only where a fake would be less clear.
- Check tests cover the intended contract without duplicating higher-level coverage.
- Check `null` tests exist only for nullable contracts or explicit Java-interoperability misuse defense.
- Check E2E class and file names use uppercase `E2E`.
- Check E2E JSON assertions target fields through `jsonPath` or parsed JSON, not raw string `contains`.
- Run the narrow test command and report the result.
