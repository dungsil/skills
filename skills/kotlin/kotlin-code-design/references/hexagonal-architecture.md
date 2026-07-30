# Hexagonal Architecture

Use this reference when a decision spans several roles or when the primary problem is dependency direction. For role-specific design, load the matching domain, use-case, adapter, shared-kernel, common/support, or Spring reference instead.

## Boundaries And Module Roles

- Identify bounded contexts from business capability, language, rules, ownership, release cadence, and integrations rather than tables or controller groups.
- Keep a model together when it changes and is reasoned about as one unit. Split only when a boundary protects behavior, ownership, dependency direction, lifecycle, or repeated wiring.
- Domain modules own domain models, value objects, invariants, domain services, and domain failures.
- Use-case modules own application orchestration, commands/queries, inbound contracts, outbound ports, and application outcomes.
- Adapter modules own REST, persistence, messaging, cache, files, schedulers, external clients, serialization, and framework mapping.
- Shared-kernel modules own only small, stable domain/application contracts deliberately shared by multiple contexts.
- Common/support modules own narrow technical reuse and must not absorb bounded-context rules.
- Runnable apps compose modules. They do not own reusable domain rules, use-case behavior, adapter mapping, or technical libraries.

## Dependency Direction

- Domain code depends on no use case, adapter, runnable app, Spring runtime, JPA, HTTP, cache, transaction, or serialization detail.
- Use cases depend inward on domain contracts and define the ports they need. They do not depend on concrete adapters.
- Adapters depend inward and translate external types and behavior into domain/application contracts.
- Apps and Spring configuration select implementations and compose the graph.
- Ports describe application needs, not adapter mechanics. Do not expose Spring Data names, JPA entities, HTTP DTOs, cache keys, table fields, joins, serialization shapes, or framework annotations.
- Keep module dependencies explicit; do not add every starter, adapter, shared, or common module to every app by default.

## Boundary Check

- A type lives at the innermost layer that can own its contract without importing outer technology.
- Mapping occurs where two contracts meet, not in the domain to save adapter code.
- Transaction, retry, cache, serialization, and framework lifecycle policies stay with the boundary that implements them unless they are explicit application requirements.
- Cross-context reuse becomes shared only after two contexts deliberately agree on the same concept; first-use code stays with its owner.
- Technical reuse goes to a named common/support module only after repeated use proves a stable contract.
