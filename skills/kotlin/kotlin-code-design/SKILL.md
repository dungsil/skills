---
name: kotlin-code-design
description: Designs, writes, refactors, or reviews idiomatic Kotlin production code and modular Spring Boot systems using Kotlin conventions, hexagonal architecture, coroutines, JVM interoperability, and explicit API contracts. Use for Kotlin design decisions involving domain models, use cases, ports, adapters, shared kernel or common modules, Spring wiring, persistence boundaries, mutability, nullability, coroutines or Flow, library compatibility, or Java callers. Do not use for simple syntax lookups or dependency installation without a design boundary.
---

# Kotlin Code Design

Use this skill when shaping Kotlin production code that should be concise for Kotlin callers, predictable at framework boundaries, and honest about its state. Keep this file as the routing guide; load the smallest role-matching reference, then add adjacent references only when a contract crosses roles.

## Workflow

1. Read the target code, nearby callers, tests, build configuration, Kotlin version, platform, JVM target, and source set before changing code.
2. Identify the primary role: Spring module/runtime composition, shared kernel, common/support module, domain, application use case or port, adapter, coroutine or Flow boundary, published API, JVM interoperability surface, or multiplatform source.
3. Load the smallest matching reference:
   - Modular Spring Boot layout, Gradle Kotlin DSL, runtime composition, configuration, and persistence boundaries: [references/spring-modular-structure.md](references/spring-modular-structure.md)
   - Cross-role hexagonal boundaries and dependency direction: [references/hexagonal-architecture.md](references/hexagonal-architecture.md)
   - Shared-kernel concepts and cross-context contracts: [references/shared-kernel.md](references/shared-kernel.md)
   - Technical common/support modules: [references/common-modules.md](references/common-modules.md)
   - Domain models, value objects, factories, and domain errors: [references/domain.md](references/domain.md)
   - Application use cases and ports: [references/domain-usecase.md](references/domain-usecase.md)
   - Persistence, REST, Spring config, and framework adapters: [references/adapter.md](references/adapter.md)
   - Core language, API, nullability, collections, and performance decisions: [references/idiomatic-kotlin.md](references/idiomatic-kotlin.md)
   - Coroutines, Flow, cancellation, dispatcher ownership, and lifecycle: [references/coroutines.md](references/coroutines.md)
   - Java callers, platform types, annotations, and binary compatibility: [references/jvm-interop.md](references/jvm-interop.md)
4. Preserve existing architectural vocabulary before introducing new abstractions.
5. Define the observable contract before convenience APIs: valid states, absence, mutation ownership, failures, cancellation, ordering, compatibility, and boundary behavior.
6. Add abstractions only when they remove repeated caller code or clarify domain intent.
7. Use companion skills when the change crosses their responsibility.
8. Format with the project's formatter and run focused verification for the changed Kotlin behavior.

## Companion Skills

- Use `$writing-kdoc` when adding or revising KDoc for public contracts, private helpers, null behavior, exceptions, symbol links, or API/test alignment.
- Use `$writing-kotlin-tests` when adding, changing, or reviewing Kotlin behavior that needs unit, integration, or E2E coverage.

## Kotlin API Use

- Prefer project-owned common/support utilities first, Kotlin and Java standard-library APIs second, and dedicated dependencies third. Do not use incidental Spring utilities as general-purpose helpers.
- Prefer role-based property names over type-repeating names when the role is obvious from the class.
- Prefer `val`, read-only collection interfaces, immutable outward-facing state, and constructor-complete objects.
- Use `data class` only for transparent value-like carriers whose equality, `copy`, destructuring, `toString`, and public construction are all valid contract semantics.
- Use value classes for type-safe scalar concepts only when validation, boxing, reflection, serialization, generics, and Java calling behavior fit.
- Use regular classes when invariants, identity, lifecycle, normalization, custom equality, framework construction, or API evolution make generated data-class semantics misleading.
- Use sealed types for closed alternatives when exhaustive handling improves the caller contract.
- Prefer expressions, default parameters, named arguments, extension functions, and standard-library operations when they improve call sites. Do not compress code past readability.
- Keep public surface area minimal. Published libraries require deliberate visibility and explicit return/property types; ordinary application internals do not need ceremonial modifiers or annotations.
- Keep one filename-matched top-level class, interface, or object per `.kt` file. Keep nested or `inner` types with their owner; allow multiple top-level declarations only in cohesive extension-only files.
- Use collections by default. Use `Sequence` only when useful laziness, multi-stage processing, early termination, or measurement offsets its overhead.
- Keep scope functions shallow and intention-revealing. Prefer named locals when receiver, side effect, or return value becomes ambiguous.
- Add JVM annotations only for a demonstrated Java caller, framework contract, or binary API need.

## Absence And Failure Contracts

- Represent legitimate single-value absence with `T?`, multi-result absence with empty collections, and richer domain outcomes with explicit result or sealed types.
- Do not introduce `Optional` into Kotlin APIs except at a required Java boundary or established local convention.
- Use `require` or `requireNotNull` for caller argument violations, `check` or `checkNotNull` for invalid object state, and domain-specific results or exceptions when callers must distinguish business outcomes.
- Keep runtime validation at trust boundaries, constructor/factory invariants, and framework or Java ingress. Do not duplicate Kotlin's non-null type contract with defensive internal checks.
- Decide explicitly whether collections may contain `null`; prefer non-null elements unless missing positions are part of the contract.
- Preserve original causes when translating infrastructure failures. Catch only exceptions that can be handled or translated meaningfully.
- Treat coroutine cancellation as control flow, not an ordinary failure value.

## Gotchas

- Do not expose mutable collections, `MutableStateFlow`, or implementation-owned coroutine scopes.
- Do not hide blocking work inside `suspend`; the boundary performing it owns the execution-context shift.
- Do not use `runCatching` around suspending work unless cancellation is rethrown and the resulting failure model is intentional.
- Do not add extension functions that obscure ownership, shadow members, or pollute a broad namespace.
- Do not use `lateinit` to avoid modeling lifecycle or initialization state.
- Do not assume Kotlin source compatibility implies JVM binary compatibility for published APIs.
- Do not put Spring stereotypes on domain types or application ports.
- Do not expose JPA entities, REST DTOs, Spring Data query names, transaction mechanics, cache keys, or serialization shapes through inner contracts.
- Do not use `shared` as a technical utility bucket or split modules only to mirror a template.

## Review Pass

Before finishing:

- Package and module roles, type names, and public functions match the actual responsibility.
- Public and cross-module contracts expose only intended states and mutability.
- Nullable types, empty values, domain failures, infrastructure failures, and cancellation have distinct meanings.
- Collection pipelines are readable and do not allocate or become lazy without reason.
- Coroutine ownership, dispatcher choice, exception propagation, and lifecycle are explicit when relevant.
- Java-facing bytecode shape and published API compatibility were checked when relevant.
- KDoc and tests were updated when API contracts or behavior changed.
- Changed Kotlin files were formatted and focused verification passed.
