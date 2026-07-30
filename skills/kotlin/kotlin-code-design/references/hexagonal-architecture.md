# Hexagonal Architecture

Use this reference when changing bounded contexts, domain models, use cases, ports, adapters, shared-kernel contracts, or technical common modules.

## Boundaries And Module Roles

- Identify bounded contexts from business capability, language, rules, ownership, and integrations rather than tables or controller groups.
- Keep a model together when it changes and is reasoned about as one unit. Split only when a boundary protects behavior, ownership, dependency direction, release cadence, or repeated wiring.
- Domain modules own domain models, value objects, invariants, domain services, and domain failures.
- Use-case modules own application orchestration, commands/queries, inbound contracts, outbound ports, and application outcomes.
- Adapter modules own REST, persistence, messaging, cache, files, schedulers, external clients, serialization, and framework mapping.
- Runnable apps compose modules. They do not own reusable domain rules, use-case behavior, or adapter mapping.

## Dependency Direction

- Domain code depends on no use case, adapter, runnable app, Spring runtime, JPA, HTTP, cache, transaction, or serialization detail.
- Use cases depend inward on domain contracts and define the ports they need. They do not depend on concrete adapters.
- Adapters depend inward and translate external types and behavior into domain/application contracts.
- Apps and Spring configuration select implementations and compose the graph.
- Ports describe application needs, not adapter mechanics. Do not expose Spring Data names, JPA entities, HTTP DTOs, cache keys, table fields, joins, serialization shapes, or framework annotations.

## Domain

- Model business concepts before storage shape. Use ubiquitous language for types, properties, and operations.
- Keep objects honest about invariants; do not expose partially valid or broadly mutable domain state.
- Use Kotlin value classes for scalar concepts when their JVM and framework constraints fit. Use data classes only when generated equality, `copy`, destructuring, and public construction are valid domain semantics.
- Prefer private or internal construction plus factories when creation validates, normalizes, selects a subtype, or can fail.
- Use nullable results for ordinary absence only when absence is the complete contract. Use explicit domain results or failures when callers need richer decisions.
- Keep domain validation failures separate from application outcomes such as not found and infrastructure failures such as corrupt persisted rows.

## Use Cases And Ports

- Keep each use case focused on application decisions: validation ownership, lookup behavior, ordering, limits, missing-result policy, and transaction-sized orchestration.
- Prefer constructor injection with minimal `val` dependencies.
- Name use cases and methods from caller intent. Name outbound ports by application role such as `Repository`, `Query`, `Gateway`, or `Client`.
- Accept raw values only where the use case owns their validation policy; convert to domain types before outbound calls.
- Return domain objects or application results, not REST responses, persistence entities, or serialized shapes.
- Use `suspend` for one-shot asynchronous work and `Flow` for streams only when those are real boundary semantics. Do not add coroutine types to pure contracts ceremonially.
- Preserve input order, cardinality, absence, and bulk-miss policy when they are observable parts of the contract.

## Adapters

- Translate at the edge: request/response DTOs, entities, framework callbacks, client payloads, and domain/application types remain distinct unless their contracts genuinely coincide.
- Map value objects to persistence or transport primitives before calling framework APIs; reconstruct domain objects through domain factories or explicit mapping policy.
- Treat persisted rows that cannot reconstruct a valid domain object as data-integrity or infrastructure failures, not as missing data.
- Keep adapter-specific filtering, joins, deleted flags, transaction APIs, retries, cache behavior, and query construction inside the adapter.
- Name implementations by port and technology or boundary, such as `<PortName>JpaAdapter` or `<PortName>HttpClient`.

## Shared Kernel And Common Support

- Reserve `shared` for small, stable domain/application concepts and policies that multiple bounded contexts deliberately agree to share.
- Promote a concept only after another context actually shares the same contract. First-use code stays with its owner.
- Put framework-light technical reuse in a narrow `common-<concern>` module.
- Put technology- or lifecycle-coupled helpers in `<concern>-support` or a focused starter.
- Do not let shared/common modules absorb bounded-context rules, use-case orchestration, adapter mapping, or app composition.
