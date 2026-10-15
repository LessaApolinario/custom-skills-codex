# Adaptive Architecture

## Repository inspection

1. Locate NestJS applications/libraries and the correct project root (`nest-cli.json`, workspaces, `src`, `apps`, `libs`).
2. Examine similar modules, their tests, and how they are imported by parent modules.
3. Identify direct NestJS services, use cases, Clean Architecture, Hexagonal Architecture/Ports & Adapters, or hybrid conventions.
4. Inspect interface/contract conventions and provider injection. Remember: TypeScript interfaces do not exist at runtime, so interface-based injection needs a runtime token.
5. Locate controllers, DTOs, entities, errors, and their naming conventions.
6. Surface inconsistent examples and ask the user to choose before introducing a different structure.

## Adaptation rules

Never impose `domain/application/infrastructure/presentation` on a project that uses `controllers/services/repositories/dto`. Do not migrate existing modules or create architectural layers merely for preference. If examples disagree, explain the alternatives and ask which pattern to follow.

## Example: conventional NestJS module (illustrative)

```text
src/users/
├── dto/
│   ├── create-user.dto.ts
│   └── get-user-params.dto.ts
├── users.controller.ts
├── users.service.ts
├── users.module.ts
├── users.controller.spec.ts
└── users.service.spec.ts
```

## Example: hexagonal module (illustrative)

```text
src/users/
├── domain/
│   ├── entities/
│   │   └── user.entity.ts
│   └── repositories/
│       └── user.repository.ts
├── application/
│   └── use-cases/
│       └── create-user.use-case.ts
├── infrastructure/
│   └── persistence/
│       └── user.repository.impl.ts
├── presentation/
│   ├── controllers/
│   │   └── users.controller.ts
│   └── dto/
│       └── create-user.dto.ts
└── users.module.ts
```

These are examples, **not templates that must be copied**. Create only files required by the approved operations and the actual architecture, plus appropriate tests.

## Layer responsibilities

- **Controllers / presentation:** transport, validation, delegation, response mapping.
- **Application / use cases:** orchestration and application rules, when this layer exists.
- **Domain:** business invariants and domain abstractions, if the architecture separates them.
- **Infrastructure:** database clients, repository implementations, gateways, and external integration adapters.
- **Module:** provider registration, dependency injection wiring, imports, and exports.

Avoid circular imports and circular provider dependencies. Respect existing exports; do not make every provider globally accessible.

## Scope example

User request: “Create a Users module with create and get-by-ID endpoints.”

Allowed: module, those two endpoints, relevant Zod contracts, services/use cases, persistence wiring where required, and appropriate tests.

Not allowed without further authorization: list, update, delete, authentication flows, extra tables, or unrelated refactors.
