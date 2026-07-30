# Domain

Use this reference when changing domain packages, including models, value objects, aggregates, factories, and domain errors.

## Responsibility

- Model business concepts first, not current table or payload shapes.
- Keep domain code free of Spring, JPA, HTTP, cache, transaction, serialization, and runtime lifecycle concerns.
- Put feature-specific violations under the context's domain error package when they are not reusable shared-kernel errors.
- Put context-level application outcomes such as not found in a sibling application exception/result package, not under domain.
- Keep entities and value objects honest about invariants; do not expose partially valid or broadly mutable domain objects.

## Factories And Validation

- Use private or internal construction plus a companion or top-level factory when creation validates, normalizes, selects a subtype, or may fail.
- Name validating factories `create(...)` when that matches local style.
- Return the project's validation or domain result type for ordinary invalid input when callers are expected to handle failure; do not throw mechanically.
- Treat validation results as closed success/failure values. Valid results expose a value, invalid results expose errors, and wrong-state access fails through terminal operations.
- Accept raw inputs when that matches nearby factories. Normalize only when normalization is part of the domain contract, then validate.
- Compose child validation results before constructing aggregates and accumulate independent errors when the caller benefits from seeing all violations.
- Use unwrap-or-throw terminal operations only after validity is established at the current boundary.

## Type Choice

- Use a `data class` only when structural equality, `copy`, destructuring, `toString`, and public construction are valid domain semantics.
- Use a regular class when invariants, identity, lifecycle, normalization, controlled mutation, custom equality, or API evolution require a narrower contract.
- Use a value class for a scalar domain distinction when boxing, generic use, reflection, persistence, serialization, validation, and Java callers remain acceptable.
- Use sealed types for closed domain alternatives that callers should handle exhaustively.
- Keep constructors private when factories are the only valid creation path.

## Naming And Members

- Name aggregates and domain models with domain nouns and ubiquitous language.
- Name value objects by the concept they protect, such as `<Thing>Id`, `<Thing>Name`, or `DisplayOrder`.
- Use `value` inside single-value objects unless a more precise domain term improves the API.
- Use domain-language property names inside aggregates and collection names that describe relationships, not storage structures.
- Use `MAX_<PROPERTY>`-style constants for domain limits when constants are part of the local convention.
- Keep raw input names close to the domain concept, then convert them to validated domain types before construction.

## Domain Errors

- Keep error values simple and data-focused.
- Use shared-kernel validation errors only for genuinely reusable cross-context violations.
- Keep domain validation failures separate from application outcomes and infrastructure failures.
- Preserve original causes only when a domain exception legitimately translates an underlying failure; infrastructure translation normally belongs in an adapter.
