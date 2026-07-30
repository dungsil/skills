# Domain Use Case

Use this reference when changing application packages: use cases, query/command ports, and application-level exceptions.

## Responsibility

- Use cases coordinate domain behavior through ports.
- Put use cases in `<context>-usecase-<usecase>` modules by default when they have meaningful behavior, dependencies, ports, or test boundaries. Keep them in `<context>-core` only for small domains where splitting would add ceremony.
- Ports live on the inner/application side and describe what the application needs, not how the outside implements it.
- Ports describe application boundaries; do not leak adapter, JPA, Spring Data, HTTP, cache, transaction, serialization, table, or query-method assumptions into them.
- Keep use cases focused on application decisions such as invalid input, lookup behavior, sorting policy, query limits, not-found handling, and transaction-sized decisions.
- Prefer constructor injection; use Lombok `@RequiredArgsConstructor` when it is enough.
- Keep pure port interfaces contract-oriented. Prefer Javadoc over forced behavior tests for interfaces with no implementation.

## Naming

- Name use cases as `<Verb><Scope><Thing>UseCase` or `<Verb><Thing>UseCase`.
- Include cardinality in use-case names when it changes behavior.
- Name command/query input carriers as `<Verb><Thing>Command` or `<Verb><Thing>Query` when a request object is useful.
- Name use-case methods from the caller's perspective, such as `get...`, `find...`, `register...`, or `change...`.
- Name ports by application need: use `Query` for read-only lookup, `Repository` for aggregate persistence semantics, and `Gateway` or `Client` for external systems.
- Keep technology words in adapter/starter classes, not core domain or use-case names.
- Name application exceptions by policy, such as invalid query, query limit exceeded, or not found.
- Put context-specific application outcome exceptions in a sibling exception package.

## Error Boundaries

- Separate invalid input from valid-but-missing results.
- Use shared-kernel application exceptions only when the policy is cross-feature, such as invalid resource queries or query-size limits.
- Do not reuse validation-error payload accessors for not-found cases.
- Use invalid-resource exceptions for malformed single-resource lookup input.
- Let ports return optional/list-shaped results for missing data; let the use case throw, filter, or preserve misses according to its public contract.

## Input And Output

- Accept raw input only at the use-case boundary when the use case owns validation policy for that input.
- Convert raw input into domain value objects before calling outbound ports.
- Decide explicitly whether invalid or missing values in bulk lookup/filter operations are excluded or rejected.
- Return domain objects or application results, not REST DTOs, JPA entities, or serialized shapes.
- Preserve input order when the public use-case contract requires it.
- Use common/support ordering helpers only for reusable mechanics. Put cross-feature ordering policy in shared-kernel contracts only when it is a deliberate policy.

## Port Shape

- Use domain value objects and domain/application result types in port signatures.
- Use `Optional<T>` for single-item lookups that may not exist.
- Use `List<T>` for multiple results where ordering may matter.
- Accept `Collection<ValueObject>` for bulk input unless a stricter collection type is part of the contract.
- Keep single and bulk lookup method names distinct in the application language.
- Document missing-result policy in Javadoc, especially whether missing bulk values are excluded.
- Do not put adapter filtering constants, deleted flags, joins, entity names, or query method names into ports.
- Do not annotate ports with Spring stereotypes.

## Fields And Constants

- Name outbound port fields by role, such as `query`, `repository`, `gateway`, or `client`.
- Name injected use-case fields by action when an adapter service composes multiple use cases.
- Use public constants only when they are part of the use-case contract.
- Name limit constants with `MAX_...`.
- Keep constructor dependencies final and minimal.

## Testing Boundary

- Test use cases with fake ports when behavior matters.
- Do not duplicate adapter mapping, repository query, HTTP routing, or JSON serialization coverage in use-case tests; use `$writing-java-tests` when test-level choice is unclear.
