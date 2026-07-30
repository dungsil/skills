# Domain

Use this reference when changing domain packages, including domain models, value objects, aggregates, and domain errors.

## Responsibility

- Model business concepts first, not current table shapes.
- Keep domain code free of Spring, JPA, HTTP, cache, transaction, and serialization concerns.
- Put domain-specific violations under the feature's domain error package when they are not reusable shared-kernel validation errors.
- Put context-level runtime exceptions for not-found or application outcomes in a sibling application/exception package, not under domain.
- Keep entities and value objects honest about invariants; do not expose partially valid domain objects.

## Factories And Validation

- Prefer static factory methods for domain objects that can fail validation. Name validating factories `create(...)` when that matches local style.
- Domain factory methods that can fail validation should return the project's validation result type rather than throw for ordinary invalid input.
- Treat validation results as closed success/failure values: valid results expose a value, invalid results expose errors, and wrong-state access fails through terminal methods.
- Accept raw inputs when that matches nearby domain factories, normalize only when normalization is part of the domain contract, then validate.
- Compose child validation results before constructing aggregate objects.
- Return accumulated errors rather than failing fast when constructing an aggregate from multiple validated children.
- Use unwrap-or-throw terminal methods only after the relevant validation results are known to be valid.

## Naming

- Name aggregate and domain model classes with domain nouns.
- Name value objects by the concept they protect, such as `<Thing>Id`, `<Thing>Name`, or `<DisplayOrder>`.
- Use `Id` for internal identifiers and a more specific suffix for external identifiers when the distinction matters.
- Name context-specific domain validation errors by the violation they represent.
- Keep names aligned with the bounded context's ubiquitous language; avoid table names unless they are also domain language.

## Design Rules

- Keep value object names aligned with domain language.
- Use records only for transparent immutable carriers; use classes when invariants, identity, normalization, custom equality, or framework-independent construction make that clearer.
- Prefer private constructors for validated domain objects and expose creation through factory methods.
- Keep domain exception classes concrete and closed unless there is a real inheritance contract.
- Override equality only when the domain contract requires it.
- Avoid helpers that hide domain logic or exist only to make a single factory look abstract.

## Fields And Methods

- Use `value` as the field name inside single-value value objects.
- Use domain-language field names inside aggregates.
- Use `MAX_<PROPERTY>` style constants for domain limits.
- Keep raw input parameter names close to the domain concept, then convert to validated value objects before construction.
- Use collection field names that express the domain relationship, not the storage structure.

## Domain Errors

- Keep error objects simple and data-focused.
- Use shared-kernel validation errors only for genuinely reusable cross-context violations.
- Keep domain validation errors separate from runtime exceptions.
