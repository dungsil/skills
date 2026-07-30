# Common Modules

Use this reference when changing technical common/support modules that are reusable but are not part of the DDD shared kernel.

## Responsibility

- Use common/support modules for technical reuse such as validation helpers, pagination mechanics, string/order/date utilities, persistence base behavior, test support, or framework integration support.
- Name modules by dependency concern or support role, such as `common-validation`, `common-pagination`, `common-ordering`, `persistence-support`, or `test-support`.
- Do not use `shared-*` for technical common modules. Keep `shared` reserved for the shared kernel.
- Avoid bare `common`; prefer a name that tells callers what dependency or behavior they import.
- Keep common/support modules narrow and smaller than feature modules.
- Do not put bounded-context domain rules, use-case orchestration, adapter mapping, or app composition into common modules.

## Common Vs Support

- Use `common-<concern>` when multiple modules directly call a stable, framework-light technical API.
- Use `<concern>-support` when the module mainly helps implement a technology, layer, framework lifecycle, fixture, or runtime concern.
- Split a module when one part can stay framework-light but another needs Spring, JPA, HTTP, cache, serialization, coroutines tied to a runtime scope, or test infrastructure.

## Dependency Policy

- Split a common module when dependency type or framework coupling would pollute other consumers.
- Framework-light common modules should not depend on Spring, JPA, HTTP, cache, serialization, or runnable apps.
- Persistence support may depend on JPA or Spring Data only for reusable persistence base behavior.
- Keep dependencies explicit so inner modules do not acquire unwanted framework, reflection, or runtime lifecycle constraints transitively.

## Utility Style

- Prefer small top-level functions for cohesive stateless operations when a namespace object adds no contract.
- Use an `object` when singleton identity, state, interface implementation, or a deliberate namespaced API is part of the design; do not recreate Java utility classes ceremonially.
- Keep extension functions narrow in visibility and natural to the receiver's public contract.
- Name helpers by contract, such as `normalize`, `hasText`, or `sortedByInput`.
- Keep similar helpers separate when their null, mutation, ordering, or failure contracts differ.

## Validation And Pagination

- Add reusable validation mechanics only after multiple contexts need the same API.
- Keep validation roles distinct: result object, validation chain, DSL state collector, rule implementation, and error model should not absorb each other's behavior.
- Name validation functions by pass conditions such as `maxLength` or `positive`.
- Type-specific nullable validation rules should skip `null` unless they are the null rule; wrong non-null types should fail explicitly.
- Keep DSL helpers as state collectors and validation logic in validators.
- Add pagination request/result mechanics only when pagination behavior is a cross-context technical contract.

## API Rules

- Express absence with nullable types or empty collections according to cardinality; do not introduce `Optional` into Kotlin common APIs.
- Snapshot mutable inputs when the public contract exposes immutable state.
- Use explicit exceptions for construction misuse and wrong-state access.
- Add common abstractions only when repeated use proves a real cross-feature technical contract.
