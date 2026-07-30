# Adapter

Use this reference when changing persistence, REST, Spring configuration, messaging, cache, external client, or other framework-facing code.

## Responsibility

- Adapters translate between external frameworks and application/domain contracts.
- Keep framework annotations, transport DTOs, persistence entities, cache details, serialization shapes, retries, and client mechanics out of domain and use-case code.
- Persistence entities map storage concerns; do not treat them as domain models by default.
- REST/OpenAPI types own request parsing and response shapes, not domain rules.

## Persistence

- Map domain value objects to persistence primitives before calling Spring Data repositories or framework clients.
- Reconstruct domain objects through domain factories or explicit mapping policy.
- Treat persisted rows that cannot reconstruct a valid domain object as data-integrity or infrastructure failures, not silent misses.
- Keep JPA repository, entity, proxy, lazy-loading, query derivation, deleted-flag, join, and transaction mechanics inside persistence adapters.
- Limit JPA-required openness, no-arg construction, mutable properties, and nullable initialization state to entity types.
- Do not use a Kotlin `data class` as a JPA entity by default.
- Use `common-persistence`, `persistence-support`, or another established support module only for genuinely reusable persistence base behavior.

## Naming

- Use `Config` for configuration classes and `Properties` for configuration-property carriers when that matches project conventions.
- Use `Controller`, `Request`, `RequestParam`, and `Response` suffixes only in REST adapter packages.
- Use `Entity`, `JpaRepository`, and `<Technology>Adapter` suffixes only in persistence adapter packages.
- Name adapter implementations `<PortName><Technology>Adapter`.
- Name port wiring configuration `<Context>PortConfig` or `<Context><Boundary>Config`.
- Name Spring Data repositories `<Entity>JpaRepository` and persistence entities `<DomainThing>Entity` when those conventions are established.
- Name a single framework repository dependency `repository`; keep technology names in adapter type names rather than core collaborator properties.

## Wiring And Runtime Boundaries

- Put port wiring configuration in the adapter or app module that provides the concrete implementation.
- Use constructor injection with non-null `val` dependencies.
- Put transaction boundaries where Spring can intercept them and where application atomicity is visible.
- Keep blocking JDBC, file, or legacy client work on the execution context owned by the adapter boundary; `suspend` alone is not non-blocking.
- Preserve coroutine cancellation when translating adapter failures.
- Keep retries, circuit breakers, cache policy, serialization, and protocol-specific error mapping at the adapter boundary unless the application contract explicitly owns the policy.

## REST And Framework Tests

- Keep JSON shape and HTTP behavior at REST or E2E boundaries.
- Keep adapter-specific unit tests for adapter policy such as forced repository filters, mapping, failure translation, or ordering guarantees.
- Use `$writing-kotlin-tests` when choosing between unit, integration, and E2E coverage.
