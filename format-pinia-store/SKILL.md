---
name: format-pinia-store
description: Create, fix, reorganize, and convert Pinia stores using a consistent pattern for JavaScript and TypeScript projects. Use when Codex needs to create a Pinia store, format an existing store, fix state/getters/actions, standardize file/composable/ID names, add getters and setters, organize Option Stores or Setup Stores, handle loading/errors in async actions, use one store inside another, or convert between Option Store and Setup Store with explicit authorization.
---

# Format Pinia Store

## Scope

Work only on the internal shape of Pinia stores:

- File name, store ID, and store composable name.
- Required imports.
- State, getters, setter-style mutation actions, and business actions.
- TypeScript typing.
- Loading handling and existing project error handling.
- Using one store inside another store.
- Conversion between Option Store and Setup Store only when explicitly authorized.

Do not define rules for consuming stores in components. Do not introduce guidance about `storeToRefs`, destructuring, `<script setup>`, `mapState`, `mapActions`, `mapStores`, persistence, Pinia plugins, HMR, SSR, Nuxt, or tests, except to preserve behavior that already exists.

## Before Editing

Consult the current official Pinia documentation before creating, fixing, reorganizing, or converting a store. Check at least store definition, Option Stores, Setup Stores, state, getters, actions, TypeScript, and store composition. Official documentation and the installed Pinia version take precedence over old examples.

Analyze the project context before changing files:

- `package.json` and the installed Pinia version.
- `tsconfig.json` or `jsconfig.json`, when present.
- ESLint, Prettier, and bundler configuration.
- Extensions used by existing stores.
- Naming conventions, import aliases, models, interfaces, services, and error handling.
- Similar stores and other stores used in the same domain.
- Store imports and consumers when renaming, removing getters, or changing the public API.

Read `references/pinia-store-rules.md` before editing a store. It contains the detailed rules for language detection, naming, Option Stores, Setup Stores, async actions, store composition, behavior preservation, and the final checklist.

## Operating Modes

### Create a store

1. Identify the Pinia version and consult the matching official documentation.
2. Detect whether the project uses JavaScript or TypeScript. If the project is mixed or ambiguous, ask which language to use.
3. Ask for the new store format: `Option Store` or `Setup Store`.
4. Identify the domain and derive the file name, composable name, and ID in camel case.
5. Analyze models, services, aliases, and existing error handling.
6. Create state, getters, mutations/setters, and actions according to the chosen format.
7. Use resource-specific loading state.
8. Do not create error state if the project has no existing pattern for it.
9. Validate imports and run the formatter, linter, typecheck, or relevant tests when available.

### Format an existing store

1. Read the entire store and preserve its current format.
2. Do not convert Option Store to Setup Store, or Setup Store to Option Store, without explicit authorization.
3. Analyze consumers before changing public names, removing redundant computeds, or renaming files.
4. Fix names, imports, typings, section order, missing getters, missing setters, and direct state changes inside actions.
5. Preserve business rules, endpoints, payloads, service contracts, error handling, persistence, SSR, HMR, and existing plugins.

### Convert a store

Convert only after explicit user authorization. Before converting, state the current format and ask whether to keep or convert it. During conversion, preserve the public API whenever possible and update affected consumers when changes are authorized.

## Essential Rules

- Use two-space indentation and respect the project's ESLint/Prettier setup.
- Use explicit blocks and explicit returns; avoid inline returns.
- In TypeScript, prefer native typing for interfaces, refs, parameters, and models. Do not simulate types in JavaScript with JSDoc.
- For the `product` domain, use `productStore.ts` or `productStore.js`, `useProductStore`, and ID `product`.
- For compound domains, use camel case: `companyOpportunityStore.ts`, `useCompanyOpportunityStore`, ID `companyOpportunity`.
- When renaming a store, update the file, composable, ID, and all affected imports.
- Business actions must call mutations/setters to change state.
- Async actions with external operations must use `try/catch/finally`, enable loading before the operation, and disable loading in `finally`.
- Reuse existing error handling. Use `console.error` only when no project pattern exists.

## Final Response

When finished, report concisely:

- Whether the store was created, fixed, reorganized, or converted.
- Language and format used.
- Files changed.
- Getters, mutations, and actions added or reorganized.
- Imports updated.
- Error handling preserved or reused.
- Validations run.
- Conflicts or risks that could not be fixed automatically.
