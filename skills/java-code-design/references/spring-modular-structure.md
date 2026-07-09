# Spring Modular Structure

Use this reference when designing, migrating, or reviewing a modular Spring Boot Gradle project.

## Baseline

- Structure modules by responsibility, not by product names copied from a template.
- Put runnable processes under `apps/`, reusable modules under `packages/`, and Gradle convention plugins under `build-logic/`.
- Keep smaller projects smaller; split modules only when the boundary protects behavior, clarifies ownership, or removes repeated infrastructure wiring.
- Replace template app names, platform prefixes, property prefixes, environment variable prefixes, organization names, and domain names with target-project names.

## Context Boundaries

- Identify bounded contexts from business capability and language, not database tables or controller groups.
- Split contexts when words, rules, release cadence, ownership, or external integrations differ independently.
- Keep a model together when its objects are always changed and reasoned about as one unit.
- Validate boundaries with event-storming outputs, use-case clusters, domain events, and policy differences.
- Keep ubiquitous language inside its bounded context; put cross-context concepts in the shared kernel only when multiple bounded contexts deliberately share them.
- Avoid creating a new bounded context for a single entity unless it has distinct language or behavior.

## Default Layout

```text
.
|-- apps/<app>/
|-- packages/shared/
|-- packages/<platform>-spring-boot-starter/
|-- packages/<platform>-spring-boot-starter-<concern>/
|-- packages/<context>-domain/
|-- packages/<context>-usecase-<usecase>/
|-- packages/<context>-adapter-rest/
|-- packages/<context>-adapter-persistence/
|-- build-logic/src/main/kotlin/
|-- gradle/
|-- gradlew
|-- gradlew.bat
|-- settings.gradle.kts
|-- build.gradle.kts
|-- gradle.properties
|-- .editorconfig
|-- .env.example
`-- .gitignore
```

## Module Roles

- `apps/<app>`: runnable application, worker, batch, migration runner, CLI, or other runtime. It composes only the starters, shared kernel, common/support modules, domain modules, use-case modules, and adapters it needs.
- Apps do not own reusable infrastructure behavior, domain rules, use-case orchestration, adapter mapping, shared-kernel contracts, or common/support utilities.
- `packages/shared`: shared kernel for small, stable concepts, value objects, contracts, and policies that multiple bounded contexts deliberately agree to share.
- Keep `shared` reserved for the DDD shared kernel. Do not use `shared-*` for technical common modules.
- Put technical common/support modules in explicitly named modules when dependency type, reuse scope, or framework coupling makes the shared kernel misleading or costly. Use `common-<concern>` for framework-light technical contracts and `<concern>-support` for technology/layer support code.
- Common modules should stay narrow: a domain-oriented common module remains independent of adapters and apps; a persistence-oriented common module may depend on JPA API and Spring Data JPA but should contain only reusable persistence base behavior.
- `packages/<context>-domain`: bounded-context domain model, value objects, domain services, domain validation errors, and domain exceptions.
- `packages/<context>-usecase-<usecase>`: one use-case slice or closely related use-case group. It owns application orchestration, commands/queries, ports, and application-level exceptions for that slice.
- Use `<context>-core` only as a deliberate small-domain consolidation when the domain model and use cases are too small to justify separate modules. Do not let `core` become the default place for everything inside the hexagon.
- `packages/<context>-adapter-<boundary>`: REST, persistence, messaging, external client, cache, file, scheduler, or other outer adapter. It depends inward on the needed domain/use-case modules and only needed shared-kernel, common/support, or starter modules.
- `packages/<platform>-spring-boot-starter[-<concern>]`: reusable Spring Boot auto-configuration for one infrastructure concern.
- `build-logic`: Gradle convention plugins for common Java, the shared-kernel module, technical commons modules, apps, and starters.

## Dependency Direction

- Apps compose modules.
- Adapters depend inward on the domain/use-case modules and only the shared-kernel, common/support, or starter modules they need.
- Domain modules do not depend on use-case modules, adapters, runnable apps, framework-specific persistence, HTTP, serialization, or Spring runtime details.
- Use-case modules depend inward on their domain module and ports, but not on concrete adapters or runnable apps.
- Starter modules expose infrastructure configuration, not domain behavior.
- The shared kernel and any common/support modules stay smaller than feature modules.
- Keep module dependencies explicit; do not add every starter or adapter to every app by default.

## Naming And Packages

- Use hyphen-case Gradle module names.
- Keep the shared kernel named `shared`. Name technical common/support modules by dependency concern or support role, such as `common-validation`, `common-pagination`, `persistence-support`, or another project-standard name.
- Name domain modules as `<context>-domain`.
- Name use-case modules as `<context>-usecase-<usecase>` when a single use-case slice has enough behavior or dependencies to stand alone.
- Reserve `<context>-core` for compact contexts where separating domain and use-case modules would add ceremony without protecting a real boundary.
- Name adapters as `<context>-adapter-<technology-or-boundary>`.
- Name owned starters as `<platform>-spring-boot-starter` and `<platform>-spring-boot-starter-<concern>`. Use a third-party technology name only when that is the actual project convention.
- Avoid generic module names such as `domain`, `api`, `infra`, or bare `common` when a bounded context or responsibility name is available.
- Use module names to show packaging responsibility and Java package names to show code responsibility.
- Keep Java package segments shorter than module names. If the module path already says adapter, prefer `<base>.<context>.rest` over `<base>.<context>.adapter.rest`.
- Use compact package shapes such as `<base>.shared.<kernel-concept>` for shared-kernel code, `<base>.common.<concern>` or `<base>.<concern>.support` for technical common/support code, `<base>.boot`, `<base>.rest`, `<base>.<context>.domain`, `<base>.<context>.application`, `<base>.<context>.persistence`, and `<base>.<app>`.

## Source Sets

- Use standard `src/main/java` and `src/test/java` by default.
- Use `src/testFixtures/java` only when fixtures must be shared across modules.
- Prefer test fixtures in the adapter module that owns the fixture type when app-level tests need persistence entities or adapter-specific fixtures.
- Do not create a separate fixture module for a single adapter's test data.

## Gradle Structure

- Keep runnable applications and packages physically separate even when Gradle project names are flat; map included projects to `apps/<app>` or `packages/<module>` with `projectDir`.
- Use `build-logic` as an included build from `settings.gradle.kts`.
- Keep module `build.gradle.kts` files declarative and short: apply one convention plugin, add module dependencies, and add only narrow extras such as `java-test-fixtures`.
- Define common Java behavior in `convention.java.gradle.kts`: Java toolchain, compiler args, encoding, JSpecify, Lombok annotation processing, JUnit, Mockito, JaCoCo, test defaults, reproducible JARs, Spring Boot BOM import, and `useJUnitPlatform()`.
- Use `<platform>.shared.gradle.kts` for the `packages/shared` shared-kernel module, `<platform>.commons.gradle.kts` for technical commons package modules such as `common-<concern>` and `<concern>-support`, `<platform>.app.gradle.kts` for runnable Spring Boot apps, and `<platform>.starter.gradle.kts` for Spring Boot starter modules.
- Concern starter conventions should include the base starter dependency and Spring Boot test support.

## Build And Environment

- Root contracts usually include `.editorconfig`, `.env.example`, `.gitignore`, `gradle.properties`, `settings.gradle.kts`, root `build.gradle.kts`, `build-logic/`, `gradle/`, `gradlew`, and `gradlew.bat`.
- Commit Gradle wrapper files so contributors and automation do not depend on a global Gradle installation.
- Use `.env.example` for documented runtime environment variables; keep real values out of version control.
- Keep explicitly managed plugin versions and non-Boot-managed dependency versions in `gradle.properties`.
- Prefer Spring Boot dependency BOM management for framework dependency versions unless the project already standardizes on a version catalog. Do not mix version catalogs and `gradle.properties` casually.
- Use plain dependency coordinates in module builds when versions come from the BOM or convention dependency management.
- Use UTF-8, LF, spaces, indent size `2`, max line length `120`, final newlines, and trimmed trailing whitespace; allow a narrow `application*.yml` line-length override when needed.
- Use `### Section ###` headers in root key-value files and keep property meanings separate, such as Gradle group, root project name, Java version, and compiler args.
- Keep `java.compiler-args=-parameters` when Spring MVC or constructor/parameter-name binding depends on Java parameter metadata.
- Ignore local env files, Gradle caches, build outputs, IDE metadata, and generated local tool files. If repo-local skills are version-controlled, ignore broad agent scratch space while explicitly allowing the skill directory.

## Spring Runtime Composition

- Keep app modules thin; put reusable Spring behavior in starter modules.
- Prefer Java configuration in starter/app config classes for Spring behavior and infrastructure wiring.
- Use `application.yml` for environment-specific scalar values or exceptional framework hooks that cannot be configured cleanly in Java.
- Prefer one small main class per runnable app. Delegate common Spring setup to a reusable boot helper or starter-provided entrypoint when the project has one.
- Use a common main annotation or meta-annotation when scan base packages and boot defaults should stay consistent. Name such annotations with a `MainClass` suffix when the project uses that convention.
- Name reusable boot helpers with an `Application` suffix when they wrap common `SpringApplication` setup.
- Centralize banner, lazy initialization, keep-alive, logging bootstrap, broad component/entity/repository scanning, datasource creation, JPA properties, repository enabling, auditing, and environment-variable fallback policy in starters/helpers instead of duplicating them in apps.
- Keep database migration execution in a dedicated runnable app when migrations need a separate lifecycle from the public/runtime app. Add migration tooling to the migration app, not every runtime app.
- Keep IDE-specific app setup out of the source of truth; the app should run from Gradle or the project wrapper.

## Starter Scope

- Give each starter one infrastructure concern, such as REST, persistence, cache, or common application bootstrap.
- Starters may include auto-configuration, default beans, framework integration, property binding, error handling, test support, and operational/documentation/observation tools directly coupled to that concern.
- Split a tool into a separate starter when it has a materially different dependency graph, lifecycle, configuration surface, or consumer set.
- Keep domain models, use-case orchestration, and bounded-context policy out of starters.
- If support code is useful without the infrastructure concern, move it to the shared kernel, a common/support module, or a narrower starter according to its responsibility.
