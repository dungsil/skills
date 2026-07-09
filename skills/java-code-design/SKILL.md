---
name: java-code-design
description: Design, write, or review Java production code and modular Spring Boot project structure using modern Java, JSpecify nullness, Lombok, Spring, JPA, domain/application/adapter boundaries, shared kernel/common module separation, multi-module Gradle apps/packages/starters, and clear public API contracts. Use only for Java design work, including domain models, use cases, ports, adapters, shared kernel, common/support modules, validation call sites, null/optional policies, public/internal APIs, or Spring module structure decisions.
---

# Java Code Design

Use this skill when shaping Java production code that should be easy to call, hard to misuse, and honest about its state. Keep this file as the routing guide; start with the smallest role-matching reference, then load adjacent references only when the module interface or seam spans domain, use case, adapter, shared kernel, or common/support responsibilities.

## Workflow

1. Read the target Java code, its tests, and nearby usage first.
2. Identify the role first: Spring module structure, shared kernel, common/support module, domain, application use case, or adapter.
3. Load the smallest matching reference by primary role; add adjacent role references only when needed to evaluate the public contract, dependency direction, or adapter seam:
   - Modular Spring Boot apps, packages, starters, and Gradle layout: [references/spring-modular-structure.md](references/spring-modular-structure.md)
   - Shared kernel concepts and cross-context contracts: [references/shared-kernel.md](references/shared-kernel.md)
   - Technical common/support modules: [references/common-modules.md](references/common-modules.md)
   - Domain models, value objects, and domain errors: [references/domain.md](references/domain.md)
   - Application use cases and ports: [references/domain-usecase.md](references/domain-usecase.md)
   - Persistence, REST, Spring config, and framework adapters: [references/adapter.md](references/adapter.md)
4. Preserve existing architectural vocabulary before introducing new abstractions.
5. Define the public contract before adding convenience methods: valid states, invalid states, null/optional policy, exception policy, and boundary behavior.
6. Add abstractions only when they remove repeated calling code or clarify domain intent.
7. Use companion skills only when the change crosses their responsibility.
8. Run focused verification for the changed Java behavior.

## Companion Skills

- Use `$writing-javadoc` when adding or revising Java Javadocs for classes, methods, private helpers, null behavior, exception contracts, or API/test alignment.
- Use `$writing-java-tests` when adding, changing, or reviewing Java behavior that needs unit, integration, or E2E coverage.

## Java API Use

- Confirm the project Java version from build files before relying on version-specific APIs.
- Prefer project-owned common/support utilities first, Java standard-library utilities second, and dedicated dependencies third. Do not use framework incidental utilities such as Spring Framework `StringUtils` as general-purpose helpers.
- Prefer role-based field names over type-repeating names when the role is obvious from the class.
- Use records for transparent immutable carriers; use classes when invariants, identity, lifecycle, or framework construction make that clearer.
- Prefer Lombok-generated constructors/getters for ordinary state access. Write them by hand only when they encode a contract, normalize inputs, derive values, adapt naming, or hide representation details.
- Mark concrete classes `final` by default when they have no explicit extension contract. Leave them non-final only for a framework proxy, documented inheritance point, sealed hierarchy, test double, or established local pattern.
- Use sealed types, pattern matching, switch expressions, collection factories, `copyOf`, and `Stream.toList()` when they improve clarity or safety.
- Keep newer features subordinate to domain clarity, persistence constraints, Spring/Jackson binding behavior, and JSpecify nullness contracts.

## Nullness Contracts

- Treat non-null as the default under JSpecify; add nullable annotations only when absence is part of the public contract.
- Prefer `Optional<T>` for nullable single-result returns, empty collections for multi-result returns, and explicit result/error types when absence carries domain meaning.
- Do not use `Optional` for fields, parameters, collection elements, or DTO properties unless the local codebase already establishes that convention.
- Keep runtime null checks at trust boundaries, constructor/factory invariants, and adapter/framework edges; do not add defensive checks that duplicate static nullness for internal non-null calls.
- Document intentional `null` acceptance or return only on public APIs and utilities where callers cannot infer it from the type.

## Review Pass

Before finishing:

- Package role, class names, and public methods match the actual responsibility.
- Null, optional, empty, boundary, and exception policies are explicit and JSpecify-compliant.
- Mutability, Lombok usage, `final`, and Java feature choices match the API contract.
- Javadocs and tests are updated when API contracts or behavior changed.
- Changed Java files are formatted and focused tests pass.
