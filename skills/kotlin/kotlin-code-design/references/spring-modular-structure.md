# Spring Modular Structure

Use this reference when designing, migrating, or reviewing a Kotlin modular Spring Boot Gradle project.

## Baseline

- Structure modules by responsibility, not a copied template. Keep small systems small; split only for a protected boundary, ownership, dependency isolation, lifecycle, release cadence, or repeated infrastructure wiring.
- Put runnable processes under `apps/`, reusable modules under `packages/`, and convention plugins under `build-logic/` when the repository follows that layout.
- Replace template app names, platform prefixes, property prefixes, environment variable prefixes, organization names, and domain names with target-project language.

## Context Boundaries

- Identify bounded contexts from business capability and language, not database tables or controller groups.
- Split contexts when words, rules, ownership, release cadence, or external integrations differ independently.
- Keep a model together when its objects are always changed and reasoned about as one unit.
- Validate boundaries with use-case clusters, domain events, policies, and integration differences.
- Keep ubiquitous language inside its bounded context. Share concepts only when multiple contexts deliberately agree on the same contract.
- Avoid creating a context for one entity without distinct language or behavior.

## Default Layout

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

- Use `<context>-core` only when a compact context gains no real protection from splitting domain and use cases.
- Name adapters by context and boundary or technology. Avoid generic `domain`, `api`, `infra`, or bare `common` names when responsibility can be explicit.
- Give each starter one infrastructure concern such as REST, persistence, cache, messaging, or application bootstrap.

## Module Roles And Dependency Direction

- Apps compose only the domain, use-case, adapter, starter, shared-kernel, and common/support modules they need.
- `packages/shared` is the DDD shared kernel, not a technical utility module.
- Use `common-<concern>` for framework-light technical contracts and `<concern>-support` for technology-, layer-, or lifecycle-coupled support.
- Domain modules remain free of Spring, JPA, HTTP, serialization, cache, and transaction dependencies.
- Use-case modules depend inward on domain contracts and ports, not concrete adapters or runnable apps.
- Adapters depend inward on domain/use-case contracts and keep framework mechanics outside inner modules.
- Starter modules provide reusable auto-configuration and framework integration, not domain rules or use-case orchestration.
- Keep shared-kernel and common/support modules narrower than feature modules and free of unwanted transitive framework dependencies.

## Naming And Packages

- Use hyphen-case Gradle module names.
- Name modules as `<context>-domain`, `<context>-usecase-<usecase>`, `<context>-adapter-<boundary>`, `common-<concern>`, or `<concern>-support` according to responsibility.
- Name owned starters `<platform>-spring-boot-starter[-<concern>]`; use third-party technology names only when that is the repository convention.
- Keep Kotlin package segments shorter than module names when the module path already carries architectural role.
- Use package names to describe code responsibility: shared-kernel concepts, common concerns, application use cases, persistence, REST, boot, and runnable apps.

## Source Sets And Gradle Structure

- Use standard Kotlin source sets: `src/main/kotlin`, `src/test/kotlin`, and `src/testFixtures/kotlin` only when fixtures are genuinely shared.
- Keep adapter-specific fixtures in the adapter that owns their framework types; do not create a fixture module for one adapter's data.
- Keep runnable applications and packages physically separate even when Gradle project names are flat; map them with `projectDir` when needed.
- Use `build-logic` as an included build and keep module `build.gradle.kts` files declarative: apply focused convention plugins, declare dependencies, and add only module-specific configuration.
- Convention plugins should centralize the Kotlin/JVM toolchain, compiler options, test platform, coverage defaults, reproducible archives, dependency management, and narrowly scoped Spring/all-open/no-arg behavior.
- Scope Kotlin Spring or JPA compiler plugins to framework-managed types. Do not make domain types broadly open or no-arg constructible.
- Concern-specific starter conventions should include the base starter contract and matching Spring Boot test support.

## Build And Environment

- Commit Gradle wrapper files so contributors and automation do not depend on a global Gradle installation.
- Keep plugin versions and non-Boot-managed dependency versions in the repository's established central source. Do not mix version catalogs, properties, and hardcoded module versions casually.
- Prefer Spring Boot dependency management for framework versions unless the repository standardizes on another single mechanism.
- Follow the repository formatter and Kotlin coding conventions; centralize charset, line endings, compiler warnings, API mode, and JVM target instead of repeating them per module.
- Keep runtime secrets out of version control. Document required variables in the repository's established example configuration.
- Ignore local environment files, Gradle caches, build outputs, IDE metadata, and generated local tool files.

## Spring Wiring

- Prefer constructor injection with non-null `val` dependencies. Avoid field injection and mutable injected properties.
- Keep application ports and domain types free of `@Component`, `@Service`, `@Repository`, `@Transactional`, and other Spring stereotypes.
- Put implementation selection and port wiring in adapter or app configuration. A concrete application service may carry a stereotype only when the project deliberately treats it as a framework entrypoint.
- Keep transaction boundaries around application operations that require atomicity. Do not leak transaction APIs into ports or domain code.
- Account for Spring proxy semantics: self-invocation bypasses advice, and private or final methods cannot be advised by ordinary subclass proxies.
- Use the Kotlin Spring/all-open plugin for established framework-managed annotations; prefer explicit collaborators over proxy-dependent internal calls.

## Persistence

- Keep JPA entities in the persistence adapter and separate from domain models by default.
- Limit JPA-required no-arg construction, proxy openness, mutable properties, lazy associations, and persistence annotations to entity types.
- Do not use a Kotlin `data class` as a JPA entity by default: generated equality, `hashCode`, `copy`, destructuring, and `toString` often conflict with identity, proxies, and lazy relationships.
- Keep Spring Data repositories and query derivation inside the adapter. Map entities through explicit mappers or domain factories.
- Place `@Transactional` where Spring can intercept it and where the application operation's atomicity is visible. Avoid annotations on private methods or self-calls.

## REST And Configuration

- REST adapters own request parsing, validation binding, status codes, headers, and response shapes. They translate to use-case inputs and outputs.
- Configuration properties own external scalar configuration, defaults, units, and validation. Prefer constructor-bound immutable Kotlin properties where framework support permits.
- Keep environment-specific values in configuration files or properties, not direct environment lookups scattered through production code.
- Use annotation use-site targets such as `@field:`, `@get:`, or `@param:` when validation, Jackson, JPA, or reflection must inspect a specific generated JVM element.

## Runtime Composition And Starter Scope

- Keep app modules thin and main functions small. Centralize reusable Spring behavior in focused starters or boot helpers.
- Keep scanning, datasource setup, JPA configuration, repository enabling, auditing, error handling, and framework defaults close to the starter or adapter that owns them.
- Use a dedicated runnable app for migrations, batches, or workers when lifecycle and dependencies differ from the runtime API.
- Add each adapter or starter only to apps that need it; do not create a universal runtime dependency graph.
- Starters may include auto-configuration, default beans, property binding, error handling, test support, and operational tools directly coupled to one infrastructure concern.
- Split a tool into another starter only when its dependency graph, lifecycle, configuration surface, or consumers materially differ.
- Move support code useful without the infrastructure concern to the shared kernel, a common/support module, or a narrower starter according to responsibility.
