# Integration Test Reference

Use this reference for tests that verify framework or persistence behavior inside the process.

## Scope

Good integration-test targets:

- JPA entity mapping.
- Spring Data repository query methods.
- Transaction and persistence lifecycle behavior.
- Spring bean wiring when the wiring itself is the contract.
- Starter auto-configuration and bean registration in the starter module that owns it.

Do not use integration tests for full user-facing HTTP contracts. Use E2E tests for those.

## Spring Persistence Tests

- Prefer Spring test slices such as `@DataJpaTest` when only JPA behavior is under test.
- Use entity/repository APIs to arrange data.
- Use fixtures and `EntityManager.persist()` when test data must be created.
- Do not use `@Sql`, raw SQL, or `JdbcTemplate.update("...")` for fixture setup unless the project already requires that pattern.

## Scope Control

- Keep repository integration tests focused on repository behavior.
- If an E2E test already proves a user-facing API can retrieve and serialize data, do not duplicate the full API path here.
- If an adapter unit test already proves call-shape policy, do not duplicate that with repository mocks here.
