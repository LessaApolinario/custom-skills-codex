# Persistence, Dependency Injection, and Errors

## Choosing the persistence technology

Detect what the project actually uses: Prisma, TypeORM, MikroORM, Drizzle, SQL drivers, external services, or no database. Never impose PostgreSQL, a new ORM, schema, migration, or connection. Inspect required schema changes and request approval before database-impacting operations.

## Repositories

- Preserve contract/implementation separation when it is part of the architecture, using appropriate dependency-injection tokens.
- Where the architecture uses concrete repositories directly, follow its conventions; do not add meaningless interfaces.
- Never access the ORM directly from controllers.
- Reuse existing persistence clients/services and register providers, imports, and exports in the correct Nest module.
- Respect provider scope and lifecycle.

## Entities and mappers

- Keep domain entities separate from ORM models when this is the established project pattern; otherwise adapt.
- Introduce mappers for meaningful transformations or explicit boundaries between domain, persistence, and transport types.
- Avoid pass-through mapper files that add no value.
- Never expose secret or sensitive database fields through API responses.

## Transactions

Use transactions only when an operation requires atomicity. Reuse the existing transaction strategy, making sure participating repositories share the appropriate transactional context. For concurrent writes, use database constraints and atomic operations rather than relying only on preliminary application-level checks. Do not automatically wrap every method in a transaction.

## Dependency injection

- Detect whether the project uses classes, string tokens, or `Symbol` tokens.
- TypeScript interfaces are erased at runtime: when injecting a contract, register a runtime token with `provide` and inject it with `@Inject(token)`.
- Ensure every new dependency is registered and imported/exported correctly.
- Avoid circular dependencies and needless global modules.

## Errors

- Follow existing domain/application/HTTP error boundaries.
- If domain errors are used, translate them to HTTP errors at an appropriate boundary instead of coupling the domain layer to NestJS.
- If the project consistently uses Nest exceptions in services, respect that pattern unless it conflicts with a stated architectural requirement.
- Never return raw database errors or expose constraint details unintentionally.
- Include failure cases and expected error mappings in tests.
