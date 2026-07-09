# Unit Test Reference

Use this reference for tests that instantiate the unit directly and verify a small public contract.

## Scope

Good unit-test targets:

- Domain value objects and aggregate factories.
- Validation and result types.
- Use-case branches.
- Entity helper methods such as `isDeleted()` and `setDeleted()`.
- Adapter call-shape policies such as forcing `activeOnly = true`.

Avoid unit tests for:

- REST controller JSON contracts.
- Spring transaction, cache, security, or MVC behavior.
- JPA mapping and repository query behavior.

## Coverage

Cover the contract from both directions:

- Happy path: successful creation/state and normal inputs that must not be rejected.
- Failure path: invalid inputs, exception paths, and accumulated error results.
- Boundaries and policies: `null` only where `@Nullable` is declared, empty values, threshold edges, type mismatch, and optional-value behavior.
- API surface: constructors/factories, public helper methods, terminal methods, and transformation methods.
- State integrity: invariants, defensive copies, and returned collection mutability when relevant.

Do not add speculative tests for behavior the class does not promise.

## Test Doubles

- Use a small fake or test record/class when the dependency is just data or a marker implementation.
- Use Mockito when interaction matters, when a collaborator is expensive, or when setting up a concrete fake would obscure the test.
- Do not mock value objects, records, simple error objects, or trivial marker interfaces only to avoid writing a two-line fake.
- Keep assertions in JUnit even when Mockito is used for setup or verification.

## Validation DSL

Use this section only when testing validation DSLs or validation result types.

- Prefer pass-condition method names: `maxLength(50)`, `positive()`, `notBlank()`.
- Keep error records named after violations, such as `TooLong`, `NonPositive`, or `Required`.
- Add success-before-failure cases.
- Add threshold boundary tests.
- Add type mismatch tests when a rule is type-specific.
- Separate terminal methods into groups such as `result` and `resultOptional`.
