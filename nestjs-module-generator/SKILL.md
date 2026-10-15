---
name: nestjs-module-generator
description: Plan, create, extend, and integrate NestJS modules using the Nest CLI, project-adaptive architecture, Zod DTOs, controllers, use cases, services, repositories, and tests. Use whenever the user requests a NestJS module, resource, endpoint, or feature, or explicitly invokes this skill. Inspect the repository first, ask for confirmation before generating code, and preserve existing project conventions.
---

# NestJS Module Generator

## Purpose

Create production-ready NestJS modules that fit the actual architecture of the repository. Generate a **complete structure for the requested scope**, but never invent CRUD operations, routes, tables, or abstractions unrelated to the request.

**Language policy:** Write all plans, questions, generated documentation, comments, sample data, test descriptions, and final reports in English. Preserve externally mandated identifiers, existing API contracts, and business terms where changing them would break compatibility.

## Non-negotiable rules

1. **Always ask before generating code.** Even for clear requests, summarize the intended scope, present a concise plan, and explicitly request permission to proceed. Combine outstanding decisions where practical. If the user has already approved a particular action, do not request identical approval again; only ask about new decisions.
2. **Inspect the entire relevant project context first:** `package.json`, lockfile, `nest-cli.json`, `tsconfig*.json`, lint configuration, workspace structure, comparable modules, persistence, authentication, Swagger, validation, tests, and conventions. Respect repository instructions such as `AGENTS.md`.
3. **Adapt rather than impose.** Do not replace the project's architecture. Explain conflicting patterns or necessary structural changes and ask which convention to follow before editing.
4. **Use official Nest CLI generators** for modules, controllers, services, and other artifacts when a suitable schematic is available. Ask for approval **before each generator command**, including its target and potentially affected files. Prefer `--dry-run` when supported, and never overwrite existing code. Avoid `nest g resource` when it would create unrequested CRUD endpoints.
5. **Ask before installing dependencies**, updating global configuration, creating/running migrations, executing destructive commands, or structurally changing shared files. Prefer the repository's package manager and local Nest CLI binary over assuming a global CLI.
6. **Use Zod for new contracts.** Create a separate DTO file for every relevant operation/contract. Export a Zod schema and a type inferred with `z.infer`. Reuse the project's validation integration; if absent, propose `nestjs-zod` and seek approval to install/configure it. Validate external `body`, `params`, and `query` input as applicable. Never claim runtime validation unless a functioning pipe/adapter is actually attached.
7. **Keep controllers thin:** accept requests, validate, delegate, and respond. Do not access the ORM directly, hold business logic, or expose secrets or sensitive fields.
8. **Implement only requested features.** Generate all infrastructure and tests necessary for the requested operations, but do not silently implement a full CRUD.
9. **Enforce consistent TypeScript:** strong typing; avoid unnecessary `any` or unsafe casts; `kebab-case` filenames; `PascalCase` classes; braces in conditionals, loops, and callbacks; explicit `return` statements in value-returning callbacks; no single-line conditional returns.
10. **Provide full testing coverage where applicable:** unit, integration, and e2e tests, using the project's existing test framework. Mock external dependencies in unit tests. Do not invent non-runnable e2e tests; clearly report environmental limitations.
11. **Verify the implementation:** run lint, typecheck, related tests, and build when the scripts/tools exist and can safely run. Never fabricate execution results.
12. **Do not introduce HTTP API versioning automatically.** Follow existing response contracts, guards/authentication, and Swagger setup. Document new endpoints when Swagger is already configured.

## Required workflow

### 1. Identify the request and scope

- Determine the intended module, requested operations, inputs/outputs, persistence, authorization, and integrations.
- A generic request for a `users` module does not authorize generating all CRUD endpoints.
- Distinguish creating a new module from extending an existing one.

### 2. Inspect the repository (read-only)

- Determine whether the project is a standalone app or monorepo, locate the correct application, and inspect its Nest CLI configuration.
- Read at least one comparable module and its tests. Check module imports/exports, provider tokens, error handling, DTOs, validation, entities, persistence, filters, guards, and Swagger.
- Identify existing files before proposing generator commands. Do not modify anything during inspection.
- Record conflicts and missing dependencies; verify installation rather than assuming.

Consult [architecture.md](references/architecture.md) and, when needed, [persistence.md](references/persistence.md).

### 3. Present a plan and ask for approval

Include a concise proposal with:

- Detected architectural pattern and repository evidence.
- Proposed file tree, distinguishing new files from modifications.
- Exact operations/endpoints, DTOs, providers, integrations, and tests.
- Proposed Nest CLI commands and their possible side effects.
- Missing dependencies, unresolved decisions, and structural changes.

**Explicitly ask** whether the user approves the plan and commands. Resolve meaningful technical ambiguities first. Approval of a plan does not imply approval of an undisclosed installation or structural change.

### 4. Implement only after approval

- Check the current files before each CLI command.
- Prefer the project's local CLI and package manager; use and review `--dry-run` when available.
- Request specific approval before **each effective** `nest generate` execution. Show the command and expected changes even when the overall plan has already been approved.
- Generate missing files only. Never overwrite existing work even if a schematic offers to do so.
- Implement domain-specific logic, schemas, dependency injection, repositories, and tests manually as required by the detected architecture.
- Keep edits to shared module imports/exports minimal; ask before structural changes.
- Do not introduce persistence, authentication, API versioning, or security mechanisms outside the approved scope.

Consult [controllers-and-dtos.md](references/controllers-and-dtos.md), [persistence.md](references/persistence.md), [testing.md](references/testing.md), and [conventions.md](references/conventions.md) as needed.

### 5. Verify

- Run available formatter/lint, typecheck, relevant unit/integration/e2e tests, and build where feasible and safe.
- Verify Nest provider resolution, module wiring, active Zod validation, HTTP response/error contracts, and obvious regressions.
- Distinguish newly introduced failures from pre-existing failures. Report both honestly.

### 6. Report

Provide a detailed English-language report covering:

1. Files created and files modified.
2. Implemented endpoints/contracts, including method, route, validation, and protection.
3. Architecture decisions and provider, persistence, and error-handling integration.
4. Executed CLI commands and installed dependencies, if authorized.
5. Actual results of tests, lint, typecheck, and build, including checks not run.
6. Remaining issues, risks, and concrete next steps.

## Reference guides

- [architecture.md](references/architecture.md) — architectural discovery and adaptation, with `users` examples.
- [controllers-and-dtos.md](references/controllers-and-dtos.md) — Zod DTOs, pipes, controllers, Swagger, HTTP responses, and authorization.
- [persistence.md](references/persistence.md) — repositories, entities, mapping, transactions, dependency injection, and errors.
- [testing.md](references/testing.md) — unit, integration, and e2e testing and verification.
- [conventions.md](references/conventions.md) — TypeScript style, naming, and Nest CLI practices.

## Official references

- Nest CLI: https://docs.nestjs.com/cli/usages
- Nest modules: https://docs.nestjs.com/modules
- Nest testing: https://docs.nestjs.com/fundamentals/testing
- Zod: https://zod.dev/
- nestjs-zod: https://github.com/BenLorantfy/nestjs-zod

External docs are supporting materials: prioritize the dependencies and conventions **actually present** in the repository.
