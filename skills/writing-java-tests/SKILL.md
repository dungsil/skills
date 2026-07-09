---
name: writing-java-tests
description: Write, improve, or review Java tests with JUnit, Mockito, Spring Boot, Spring Data JPA, and JSpecify across unit, integration, and E2E levels. Use only for Java test work, including domain, use-case, entity, validation, repository, adapter, HTTP API, nullability, or Java test-level selection decisions.
---

# Writing Test

Use this skill to choose the right test level first, then write compact contract-focused Java tests.

Use JUnit APIs for assertions and test structure. Mockito is allowed when interaction matters, but keep assertions in JUnit. Do not introduce AssertJ, Hamcrest, Truth, or other assertion libraries unless the project already uses them. For E2E JSON body checks, use `jsonPath`; Hamcrest matchers are allowed only inside `jsonPath` assertions.

## Workflow

1. Read the target code and nearby tests before writing anything.
2. Decide the lowest useful test level: unit, integration, or E2E.
3. Load the matching reference only when needed:
   - Unit tests: [references/unit.md](references/unit.md)
   - Integration tests: [references/integration.md](references/integration.md)
   - E2E tests: [references/e2e.md](references/e2e.md)
4. Identify the public contract and add the smallest useful test set.
5. Avoid duplicate coverage across levels.
6. Put unit and integration tests in the module that owns the behavior.
7. Run the narrowest relevant test command first, then broader tests when risk justifies it.

## Test Levels

Choose the lowest useful level:

- Domain value object, entity helper method, use-case branch, repository adapter call shape: unit test.
- JPA entity mapping, repository query method, transaction/lifecycle behavior: integration test.
- HTTP route, serialization, security filter, controller-service-use-case-repository flow: E2E test.

Avoid duplicate coverage:

- If E2E verifies HTTP -> repository -> domain -> JSON, do not also assert every mapped domain field in adapter unit tests.
- Keep adapter unit tests only for adapter-specific policy, such as forcing `activeOnly = true` on repository calls.
- Keep entity unit tests for pure entity helper contracts such as `isDeleted()` and `setDeleted()`.

## Test Fixtures

- Use Gradle `java-test-fixtures` only when fixtures must be shared across modules.
- Prefer fixtures in the adapter module that owns the fixture type.
- App-level E2E or integration tests may depend on `testFixtures(project(":<adapter-module>"))` when they need adapter-owned entity fixtures.
- Do not create a separate fixture module for a single adapter's test data.

## Common Structure

Prefer `@Nested` groups when a class has multiple meaningful behavior surfaces.

If there is only one meaningful group, and the class is unlikely to expand soon, keep the test cases flat in the test class. Do not create a single `@Nested` group only for ceremony.

Within a group or flat class, place successful/valid cases first, then boundary cases, then invalid/exception cases.

Use `@BeforeEach` when multiple test cases repeat the same setup or fixture creation. Keep shared preconditions in setup and behavior-specific values inside the test method.

## Naming

- Prefer `shouldBe...` method names.
- Use `shouldBeRejectedWhen...` for invalid inputs.
- Use `shouldBeReturned...When...` for getter/helper return behavior.
- Use `shouldBeThrownWhen...` for expected exception paths.
- Keep detailed behavior text in `@DisplayName`; match the repository's language and style.
- Always write E2E with uppercase `E2E` in class and file names, such as `UserProfileE2ETest`.

## Assertions

Use `assertAll` when one action produces a state with multiple observable properties.

Avoid asserting exception messages unless the message is part of the public contract.

## Nullability Policy

Respect JSpecify nullability contracts. Treat declared annotations as the source of truth.

- A parameter, field, or return type without `@Nullable` is non-null under JSpecify defaults such as `@NullMarked`. Do not write tests that pass `null` to those parameters or expect `null` from those returns.
- A parameter, field, or return type with `@Nullable` may be `null`. Cover the `null` case explicitly, including the policy encoded by the production code.
- If existing code is missing `@Nullable` but actually accepts `null`, fix the source annotation first, then write the test.
- When verifying defensive behavior against a contract violation, suppress static checks with `@SuppressWarnings("DataFlowIssue")` and make the intent clear in `@DisplayName`.
- Annotate test helpers, fakes, and fixtures with JSpecify annotations to match the production contract. Do not relax nullability in test doubles.

## Review Pass

Before finishing:

- Check the chosen test level is the lowest useful level.
- Check level-specific reference rules were followed.
- Check success cases appear before failure cases.
- Check method names follow project conventions.
- Check tests cover the intended contract without duplicating higher-level coverage.
- Check `null` tests exist only for declared nullable contracts or explicit misuse defense tests.
- Check E2E class/file names use uppercase `E2E`.
- Check E2E JSON body assertions use `jsonPath`.
- Run the narrow test command and report the result.
