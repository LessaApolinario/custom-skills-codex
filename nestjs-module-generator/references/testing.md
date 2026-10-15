# Testing and Verification Strategy

## Framework and scope

Detect Jest, Vitest, or another configured framework using `package.json`, test config, and actual test files. Do not add a second framework without approval. Create unit, integration, and e2e tests when applicable to the requested operations and available infrastructure. Avoid empty tests and superficial “module is defined” checks.

## Unit tests

- Test business rules, services, use cases, nontrivial mappers, and failure paths.
- Mock ORM, networking, time, queues, and external services.
- Cover inputs, outputs, exceptions, and edge cases, using strongly typed mocks.
- Prefer Arrange / Act / Assert when it improves clarity.
- Do not use real database connections in unit tests.

## Integration tests

- Verify provider wiring, repositories, and persistence behavior where relevant, using the repository's existing test database strategy.
- Never point tests to production databases or use real production credentials.
- Ensure isolation, setup/teardown, and rollback/cleanup where needed.
- Do not assume Docker or Testcontainers exists; explain missing requirements.

## E2E tests

- Make real HTTP requests against the test application to verify Zod validation for `body`, `params`, and `query`, routing, status codes, response contracts, and authorization.
- Cover valid requests, invalid input, and expected error cases.
- Use the project's existing e2e harness (e.g. Supertest) and fixtures.
- If e2e infrastructure is absent, ask before introducing new dependencies/configuration; report tests not created or executed and explain why.

## Final checks

1. Run the project's formatter or lint task, if available.
2. Run typecheck with the actual project configuration, if available.
3. Run relevant unit and integration tests.
4. Run relevant e2e tests when practical and safe.
5. Run the build when supported and safe.
6. Review provider imports/exports, Zod pipes, authorization, Swagger documentation, and all affected routes.

Report exact command outcomes and clearly distinguish passed, failed, skipped, and unavailable checks. Do not claim that tests pass if they were not executed.
