# Common Modules

Use this reference when changing technical common/support modules that are reusable but are not part of the DDD shared kernel.

## Responsibility

- Use common/support modules for technical reuse such as validation helpers, pagination mechanics, string/order/date utilities, persistence base behavior, test support, or framework integration support.
- Name modules by dependency concern or support role, such as `common-validation`, `common-pagination`, `common-persistence`, `persistence-support`, or another project-standard name.
- Do not use `shared-*` for technical common modules. Keep `shared` reserved for the shared kernel.
- Avoid bare `common`; prefer a name that tells callers what dependency or behavior they are importing.
- Keep common/support modules narrow and smaller than feature modules.
- Do not put bounded-context domain rules, use-case orchestration, adapter mapping, or app composition into common modules.

## Common vs Support

- Use `common-<concern>` when multiple modules directly call the API as a stable technical contract: `common-validation`, `common-pagination`, `common-ordering`.
- Use `<concern>-support` when the module mainly helps implement a specific technology, layer, or runtime concern: `persistence-support`, `rest-support`, `test-support`.
- Prefer `common-<concern>` for framework-light primitives that domain, use-case, adapter, or starter modules can safely depend on.
- Prefer `<concern>-support` when the code intentionally carries framework dependencies, lifecycle assumptions, fixtures, or adapter/starter implementation details.
- Split the module when one part can stay framework-light but another part needs Spring, JPA, HTTP, cache, serialization, or test infrastructure.

## Dependency Policy

- Split a common module when dependency type or framework coupling would pollute other consumers.
- Framework-light common modules should not depend on Spring, JPA, HTTP, cache, serialization, or runnable apps.
- Persistence support modules may depend on JPA API or Spring Data JPA, but only for reusable persistence base behavior.
- Common modules may be used by shared kernel, domain, use-case, adapter, starter, or app modules only when the dependency direction stays explicit and does not pull in unwanted framework dependencies.

## Utility Style

- Use `@UtilityClass` only for small stateless utility groups with cohesive responsibility.
- Keep utility method names contract-oriented, such as `normalize`, `hasText`, or `sortByInput`.
- Document null behavior when a utility intentionally accepts `null`.
- Keep similar helpers separate when their contracts differ; do not mechanically alter one because another changed.

## Validation And Pagination

- Put reusable validation mechanics in a validation-oriented common module only when multiple contexts need the same API.
- Keep validation roles distinct: result object, validation chain, DSL helper, and error model should not absorb each other's behavior.
- Name validator methods by pass conditions such as `maxLength` or `positive`.
- Type-specific validation rules should skip `null` unless they are the null rule; wrong non-null types should fail explicitly.
- Keep DSL helpers as state collectors. Put validation logic in the validator.
- Put pagination request/result mechanics in a pagination-oriented common module only when pagination behavior is a cross-context technical contract.

## API Rules

- Respect JSpecify nullness before adding runtime null checks.
- Defensively copy mutable inputs when a common type exposes immutable semantics.
- Use explicit exception types for construction misuse and wrong-state access.
- Add common abstractions only when repeated use shows a real cross-feature technical contract.
