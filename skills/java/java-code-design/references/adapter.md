# Adapter

Use this reference when changing persistence, REST, Spring config, or other framework-facing packages.

## Responsibility

- Adapters translate between external frameworks and application/domain contracts.
- Keep framework annotations, HTTP DTOs, persistence entities, cache details, and serialization concerns out of domain and use-case code.
- Persistence entities map storage concerns; do not treat them as the domain model by default.
- REST/OpenAPI classes own request parsing and response shapes, not domain rules.

## Persistence

- Map domain value objects to persistence primitives before calling repositories or clients.
- Reconstruct domain objects through domain factories or explicit mapping policies.
- Persisted invalid rows are infrastructure/data-integrity failures, not silent misses. Throw an infrastructure/data-integrity exception when a persisted record cannot be reconstructed as a valid domain object.
- Keep JPA repository and entity assumptions in persistence adapters.
- Use `common-persistence`, `persistence-support`, or another project-standard common/support module only for genuinely reusable persistence base behavior.

## Naming

- Use `Config` for Java configuration classes and `Properties` for configuration property carriers.
- Use `Controller`, `RequestParam`, and `Response` suffixes only in REST adapter packages.
- Use `Entity`, `JpaRepository`, and `<Technology>Adapter` suffixes only in persistence adapter packages.
- Name adapter implementations as `<PortName><Technology>Adapter`.
- Name port wiring config as `<Context>PortConfig` or `<Context><Boundary>Config`.
- Name Spring Data repositories as `<Entity>JpaRepository` and persistence entities as `<DomainThing>Entity`.
- Name a single framework repository dependency `repository`.
- Use short role names for collaborators in core code; keep technology names in adapter class names.

## Wiring

- Put port wiring config in the adapter module that provides the concrete implementation.

## REST And Framework Tests

- Keep JSON shape and HTTP behavior at REST/E2E boundaries.
- Keep adapter-specific unit tests for adapter policy only, such as forced repository filters or mapping rules.
- Use `$writing-java-tests` when choosing between unit, integration, and E2E coverage.
