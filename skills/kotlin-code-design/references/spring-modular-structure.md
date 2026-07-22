# Spring Modular Structure

Use this reference when designing, migrating, or reviewing a Kotlin modular Spring Boot Gradle project.

## Baseline

- Structure modules by responsibility, not a copied template. Keep small systems small; split only for a protected boundary, ownership, dependency isolation, lifecycle, or repeated infrastructure wiring.
- Put runnable processes under `apps/`, reusable modules under `packages/`, and convention plugins under `build-logic/` when the repository follows that layout.
- Use standard Kotlin source sets: `src/main/kotlin`, `src/test/kotlin`, and `src/testFixtures/kotlin` only when fixtures are genuinely shared.
- Keep module `build.gradle.kts` files declarative: apply a focused convention plugin, declare dependencies, and add only module-specific configuration.

## Suggested Roles

```text
apps/<app>/
packages/shared/
packages/common-<concern>/
packages/<concern>-support/
packages/<context>-domain/
packages/<context>-usecase-<usecase>/
packages/<context>-adapter-rest/
packages/<context>-adapter-persistence/
packages/<platform>-spring-boot-starter[-<concern>]/
build-logic/
```

- Use `<context>-core` only when a small context would gain no real protection from splitting domain and use cases.
- Name adapters by context and boundary or technology. Avoid generic `domain`, `api`, `infra`, or bare `common` names when responsibility can be explicit.
- Give each starter one infrastructure concern such as REST, persistence, cache, messaging, or application bootstrap.

## Dependency Direction

- Apps compose only the domain, use-case, adapter, starter, shared-kernel, and common/support modules they need.
- Adapters depend inward on domain/use-case contracts. Domain and use-case modules never depend on adapters or runnable apps.
- Domain modules remain free of Spring, JPA, HTTP, serialization, cache, and transaction dependencies.
- Starter modules provide reusable auto-configuration and framework integration, not domain rules or use-case orchestration.
- Framework-light common modules must not pull Spring/JPA/serialization dependencies into inner modules.

## Spring Wiring

- Prefer constructor injection with non-null `val` dependencies. Avoid field injection and mutable injected properties.
- Keep application ports and domain types free of `@Component`, `@Service`, `@Repository`, `@Transactional`, and other Spring stereotypes.
- Put implementation selection and port wiring in adapter or app configuration. A concrete application service may carry a stereotype only when the project deliberately treats it as a framework entrypoint.
- Keep transaction boundaries around application operations that require atomicity. Do not leak transaction APIs into ports or domain code.
- Account for Spring proxy semantics: self-invocation bypasses proxy advice, and private/final methods cannot be advised by ordinary subclass proxies. Prefer explicit collaborators over proxy-dependent internal calls.
- Kotlin classes and methods are final by default. Use the Kotlin Spring/all-open plugin for framework-managed annotated types when established; never make domain types broadly `open` for Spring.

## Persistence

- Keep JPA entities in the persistence adapter and separate from domain models by default.
- Limit JPA-required no-arg construction, proxy openness, mutable properties, lazy associations, and persistence annotations to entity types.
- Do not use a Kotlin `data class` as a JPA entity by default: generated equality, `hashCode`, `copy`, destructuring, and `toString` often conflict with identity, proxies, and lazy relationships.
- Keep repositories and query derivation inside the adapter. Map entities through explicit mappers or domain factories.
- Place `@Transactional` where Spring can actually intercept it and where the application operation's atomicity is visible. Avoid relying on annotations on private methods or self-calls.

## REST And Configuration

- REST adapters own request parsing, validation binding, status codes, headers, and response shapes. They translate to use-case inputs and outputs.
- Configuration properties own external scalar configuration, defaults, units, and validation. Prefer constructor-bound immutable Kotlin properties where framework support permits.
- Keep environment-specific values in configuration files or properties, not direct environment lookups scattered through production code.
- Use annotation use-site targets such as `@field:`, `@get:`, or `@param:` when validation, Jackson, JPA, or reflection must inspect a specific generated JVM element.

## Runtime Composition

- Keep app modules thin and main classes small. Centralize reusable Spring behavior in focused starters or boot helpers.
- Keep scanning, datasource setup, JPA configuration, repository enabling, auditing, error handling, and framework defaults close to the starter or adapter that owns them.
- Use a dedicated runnable app for migrations or batches when their lifecycle and dependencies differ from the runtime API.
- Add each adapter or starter only to apps that need it; do not create a universal runtime dependency graph.
