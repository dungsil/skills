# Domain Use Case

Use this reference when changing application packages: use cases, commands/queries, ports, and application-level outcomes.

## Responsibility

- Use cases coordinate domain behavior through ports.
- Put use cases in `<context>-usecase-<usecase>` modules when they have meaningful behavior, dependencies, ports, or test boundaries. Keep them in `<context>-core` only when splitting a small context would add ceremony.
- Ports live on the inner/application side and describe what the application needs, not how an adapter implements it.
- Keep Spring Data, JPA, HTTP, cache, transaction APIs, serialization, table, query-method, and framework assumptions out of ports.
- Keep use cases focused on application decisions such as validation ownership, lookup behavior, ordering, limits, missing-result policy, and transaction-sized orchestration.
- Prefer constructor injection with minimal non-null `val` dependencies.
- Keep pure port interfaces contract-oriented. Prefer KDoc over forced behavior tests for interfaces with no implementation.

## Naming

- Name use cases `<Verb><Scope><Thing>UseCase` or `<Verb><Thing>UseCase` and include cardinality when it changes behavior.
- Name input carriers `<Verb><Thing>Command` or `<Verb><Thing>Query` when a request object improves the boundary.
- Name functions from caller intent, such as `get`, `find`, `register`, or `change`.
- Name ports by application need: `Query` for read-only lookup, `Repository` for aggregate persistence semantics, and `Gateway` or `Client` for external systems.
- Keep technology words in adapter or starter types, not domain or use-case names.
- Name application outcomes and exceptions by policy, such as invalid query, query limit exceeded, or not found.

## Error Boundaries

- Separate invalid input from valid-but-missing results.
- Use shared-kernel application outcomes only when the policy is genuinely cross-context.
- Let single-result ports return `T?` for ordinary absence and collection-shaped results for multi-result queries.
- Let the use case throw, return a result, filter, or preserve misses according to its public contract.
- Do not reuse validation-error accessors or sentinel values for not-found outcomes.

## Input And Output

- Accept raw input only when the use case owns validation policy for it.
- Convert raw input into domain types before calling outbound ports.
- Decide explicitly whether invalid or missing values in bulk lookup/filter operations are excluded, preserved, or rejected.
- Return domain objects or application results, not REST DTOs, JPA entities, or serialized shapes.
- Preserve input order when the public contract requires it.
- Use `suspend` for one-shot asynchronous work and `Flow` for streams only when those are real boundary semantics, not ceremonial coroutine types.

## Port Shape

- Use domain values and domain/application result types in port signatures.
- Use `T?` for single-item lookups that may not exist and `List<T>` when multi-result ordering may matter.
- Accept `Collection<ValueObject>` for bulk input unless a stricter collection type is part of the contract.
- Keep single and bulk lookup function names distinct in application language.
- Document missing-result policy in KDoc when callers cannot infer it, especially for bulk operations.
- Do not annotate ports with Spring stereotypes.

## Fields And Constants

- Name outbound port properties by role, such as `query`, `repository`, `gateway`, or `client`.
- Name injected use-case properties by action when an adapter service composes several use cases.
- Expose constants only when they are part of the use-case contract.
- Name limit constants with `MAX_...` when that matches local style.

## Testing Boundary

- Test use cases with fake ports when behavior matters.
- Do not duplicate adapter mapping, repository query, HTTP routing, or JSON coverage in use-case tests; use `$writing-kotlin-tests` when test-level choice is unclear.
