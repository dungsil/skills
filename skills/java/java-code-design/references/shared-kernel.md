# Shared Kernel

Use this reference when changing the DDD shared kernel: small, stable concepts, value objects, contracts, and policies deliberately shared by multiple bounded contexts.

## Responsibility

- Start with one shared kernel module for small, stable concepts, value objects, contracts, and policies that multiple bounded contexts deliberately agree to share.
- Keep `shared` reserved for the DDD shared kernel. Put technical common code in explicitly named common/support modules.
- Do not turn the shared kernel into a catch-all utility bucket. If a rule is specific to one feature or domain concept, keep it in that feature.
- Keep public shared-kernel APIs small, stable, and easy to discover from call sites.
- Promote code to shared only after another bounded context actually reuses the same contract or primitive. Keep first-use behavior in the owning context.
- Validation or pagination types may live under `shared` only when they express agreed cross-context domain/application contracts; technical helpers and persistence base behavior belong in the owning context or a narrower common/support module.

## Contract Shape

- Respect JSpecify nullness before adding runtime null checks.
- Defensively copy mutable inputs when a shared-kernel type exposes immutable semantics.
- Use explicit exception types for construction misuse and wrong-state access.
- Preserve current result contracts, nullness policy, and error model shape unless the requested change explicitly changes them.
- Use closed success/failure results for domain factory and parser outcomes when that result type is part of the shared kernel.

## Naming

- Name shared-kernel concepts with the ubiquitous language shared by the bounded contexts.
- Name shared result types with nouns that describe their contract.
- Name reusable validation error types by violations, not validators or callers.
- Name shared domain/application exceptions by policy, such as invalid query or query limit exceeded.
