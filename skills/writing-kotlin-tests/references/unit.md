# Unit Test Reference

Use this reference for tests that instantiate the unit directly and verify a small public contract.

## Scope

Good unit-test targets:

- Domain value classes, data classes with genuine value semantics, and aggregate factories.
- Validation and result types.
- Use-case branches.
- Entity helper methods such as `isDeleted()` and `setDeleted()`.
- Adapter call-shape policies such as forcing `activeOnly = true`.
- Pure coroutine or Flow transformations whose scheduling is not framework-owned.

Avoid unit tests for:

- REST controller JSON contracts.
- Spring transaction, cache, security, MVC, or proxy behavior.
- JPA mapping and repository query behavior.

## Coverage

Cover the contract from both directions:

- Happy path: successful creation or state and normal inputs that must not be rejected.
- Failure path: invalid inputs, exception paths, and accumulated error results.
- Boundaries and policies: `null` only for nullable types, empty values, threshold edges, type mismatch, and optional-value behavior.
- API surface: constructors or factories, public helpers, terminal operations, and transformations.
- State integrity: invariants, defensive copies, and returned collection mutability when relevant.

Do not add speculative tests for behavior the type does not promise.

## Test Doubles

- Use a small fake or object expression when the dependency is just data or a marker implementation.
- Use MockK when interaction matters, a collaborator is expensive, or a concrete fake would obscure the test.
- Do not mock value objects, simple result or error types, or trivial marker interfaces only to avoid a two-line fake.
- Avoid relaxed mocks by default; explicitly stub calls the contract uses so unexpected calls fail.
- Use `every` and `verify` for ordinary calls, and `coEvery` and `coVerify` for suspending calls.
- Specify `verify(exactly = n)` only when call count is part of the contract. Use `confirmVerified` only when no additional interactions is itself required behavior.
- Keep state and value assertions in `kotlin.test` even when MockK is used for setup or verification.

## Coroutines And Flow

- Call a suspend function directly from the project's coroutine test scope; do not use sleeps or real delays.
- Use virtual time only when delay, timeout, retry, or scheduling behavior is the contract.
- Test emitted values, completion, failure, cancellation, and ordering as applicable; do not assert coroutine implementation details.
- Inject dispatchers or scopes only when production ownership must be controlled. Do not redesign production code solely to satisfy a test.

## Validation DSL

Use this section only when testing validation DSLs or validation result types.

- Prefer pass-condition method names such as `maxLength(50)`, `positive()`, and `notBlank()`.
- Keep error types named after violations, such as `TooLong`, `NonPositive`, or `Required`.
- Add success-before-failure cases.
- Add threshold boundary tests.
- Add type mismatch tests when a rule is type-specific.
- Separate terminal operations into groups such as `result` and `resultOrNull` when their contracts differ.
