---
name: kotlin-code-design
description: Designs, writes, refactors, or reviews idiomatic Kotlin production code and modular Spring Boot systems using Kotlin conventions, hexagonal architecture, coroutines, JVM interoperability, and explicit API contracts. Use for Kotlin design decisions involving domain models, use cases, ports, adapters, shared kernel or common modules, Spring wiring, persistence boundaries, mutability, nullability, coroutines or Flow, library compatibility, or Java callers. Do not use for simple syntax lookups or dependency installation without a design boundary.
---

# Kotlin Code Design

Use this skill to keep Kotlin code explicit at boundaries, concise inside them, and predictable for callers. Preserve established repository conventions unless they violate correctness or the requested contract.

## Workflow

1. Read the target code, nearby callers, tests, build configuration, Kotlin version, platform, and source set before changing code.
2. Identify the code's role: Spring module/runtime composition, shared kernel, common/support module, domain, application use case or port, adapter, coroutine or Flow boundary, published library API, JVM interoperability surface, or multiplatform source.
3. Load only the matching reference:
   - Core language, API, nullability, collections, and performance decisions: [references/idiomatic-kotlin.md](references/idiomatic-kotlin.md)
   - Coroutines, Flow, cancellation, dispatcher ownership, and lifecycle: [references/coroutines.md](references/coroutines.md)
   - Java callers, platform types, annotations, and binary compatibility: [references/jvm-interop.md](references/jvm-interop.md)
   - Hexagonal boundaries, domain, use cases, ports, adapters, shared kernel, and common/support modules: [references/hexagonal-architecture.md](references/hexagonal-architecture.md)
   - Modular Spring Boot layout, Gradle Kotlin DSL, runtime composition, configuration, and persistence boundaries: [references/spring-modular-structure.md](references/spring-modular-structure.md)
4. State the observable contract: valid states, absence, mutation ownership, failures, cancellation, ordering, and compatibility constraints.
5. Implement the smallest design that makes invalid states difficult and keeps platform or framework concerns at their boundary.
6. Format with the project's formatter and run the narrowest verification that exercises the changed behavior.

## Kotlin Defaults

- Prefer `val`, read-only collection interfaces, immutable outward-facing state, and constructor-complete objects.
- Represent legitimate absence with nullable types. Prefer safe calls, Elvis, smart casts, or explicit branching; treat `!!` as a boundary assertion requiring a visible invariant.
- Use `data class` for transparent value-like carriers, value classes for type-safe scalar concepts when their boxing and interoperability limits fit, and sealed hierarchies for closed alternatives.
- Prefer expressions, default parameters, named arguments, extension functions, and standard-library operations when they improve the caller's code. Do not compress code past readability.
- Prefer `require` for invalid arguments, `check` for invalid object state, and domain-specific results or exceptions when failure is part of the domain contract.
- Keep public surface area minimal. Published libraries require explicit visibility and return/property types; ordinary application internals do not need ceremonial modifiers or type annotations.
- Use collections by default. Use `Sequence` only when lazy multi-stage processing or early termination offsets its overhead.
- Keep scope functions shallow and intention-revealing. Avoid nested or long chains where `this`, `it`, side effects, or return values become ambiguous.
- Preserve structured concurrency. Never introduce `GlobalScope` for ordinary work or swallow cancellation.
- Add JVM annotations only for a demonstrated Java caller or framework contract, not to imitate Java syntax.

## Gotchas

- Do not convert every class to a `data class`; generated `copy`, destructuring, equality, and public constructors are part of the contract.
- Do not expose mutable collections, `MutableStateFlow`, or implementation-owned scopes.
- Do not hide blocking work inside `suspend`; move it to an owned execution context at the boundary that performs it.
- Do not use `runCatching` around suspending work unless cancellation is rethrown and the resulting failure model is intentional.
- Do not add extension functions that obscure ownership, shadow members, or pollute a broad namespace.
- Do not use `lateinit` to avoid modeling lifecycle or initialization state.
- Do not assume source compatibility implies JVM binary compatibility for published APIs.
- Do not put Spring stereotypes on domain types or application ports.
- Do not expose JPA entities, REST DTOs, Spring Data query names, transaction mechanics, or serialization shapes through inner contracts.
- Do not use `shared` as a technical utility bucket or split modules only to mirror a template.

## Review Pass

Before finishing:

- Public and cross-module contracts expose only intended states and mutability.
- Nullable types, empty values, failures, and cancellation have distinct, deliberate meanings.
- Collection pipelines are readable and do not allocate or become lazy without reason.
- Coroutine ownership, dispatcher choice, exception propagation, and lifecycle are explicit.
- Java-facing bytecode shape and published API compatibility were checked when relevant.
- Module roles, dependency direction, Spring wiring, persistence mapping, and transaction boundaries stay on the correct side of the hexagon.
- Changed behavior was exercised by a focused test, build, or runnable scenario.
