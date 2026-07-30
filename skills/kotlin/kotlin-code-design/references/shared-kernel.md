# Shared Kernel

Use this reference when changing the DDD shared kernel: small, stable concepts, value objects, contracts, and policies deliberately shared by multiple bounded contexts.

## Responsibility

- Start with one shared-kernel module for small, stable concepts, value objects, contracts, and policies that multiple bounded contexts deliberately agree to share.
- Keep `shared` reserved for the DDD shared kernel. Put technical common code in explicitly named common/support modules.
- Do not turn the shared kernel into a catch-all utility bucket. Keep feature-specific rules with their owning context.
- Keep public shared-kernel APIs small, stable, and easy to discover from Kotlin call sites.
- Promote code only after another bounded context actually reuses the same contract or primitive. Keep first-use behavior in the owning context.
- Validation or pagination types belong in `shared` only when they express agreed cross-context domain/application contracts; technical mechanics and persistence support belong elsewhere.

## Contract Shape

- Use Kotlin nullable types to express legitimate absence and non-null types for required values.
- Copy mutable inputs with `toList`, `toSet`, or another appropriate snapshot when a shared type promises immutable outward-facing state.
- Use sealed results for closed success/failure outcomes when callers must branch on domain construction or parsing failures.
- Use explicit exception types for construction misuse and wrong-state access when exceptions are the established contract.
- Preserve current result shape, absence policy, mutability, and error model unless the requested change explicitly changes them.
- Consider Java-facing bytecode and binary compatibility before exposing value classes, data classes, default parameters, or inline APIs from a published shared kernel.

## Naming

- Name shared-kernel concepts with the ubiquitous language shared by the bounded contexts.
- Name shared result types with nouns that describe their contract.
- Name reusable validation errors by violations, not validators or callers.
- Name shared domain/application exceptions by policy, such as invalid query or query limit exceeded.
