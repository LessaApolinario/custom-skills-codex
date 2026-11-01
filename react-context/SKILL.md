---
name: react-context
description: Create, implement, adapt, or modify React Context providers and individual custom hooks from natural-language requirements. Use for React Context API or use-context-selector work, including converting existing contexts to a three-file provider/hooks/index architecture in kebab-case directories, with Next.js and TypeScript/JavaScript awareness. Do not trigger for unrelated state-management libraries unless React Context is requested.
---

# React Context

Build and maintain React contexts following the user's three-file architecture. Work in English for code comments and explanatory text unless the repository requires another language. Follow the repository's existing formatting and conventions rather than copying example formatting verbatim.

## Non-negotiable architecture

For a **new context**, create a **kebab-case** directory in the location that matches the repository's existing organization:

```text
active-screen/
  ActiveScreenProvider.tsx
  hooks.ts
  index.ts
```

Use `.jsx` for the provider and `.js` for the other files in JavaScript projects. The provider is a named function component. `index.ts` defines/exports the context and its interface; it does **not** re-export hooks. `hooks.ts` exports one named hook per public state value, derived value, and action. Use semantic action names (e.g. `navigateToScreen`, `addItem`, `clearCart`) rather than generic setters unless the requirements explicitly call for a setter. Preserve the `<Context.Provider value={...}>` style. Introduce extra files only for a justified complexity, retaining these three core files.

For an **existing context**, support both feature changes and **adaptation into this architecture**. Inspect the original implementation and consumers; propose any conversion to the three-file layout, including the kebab-case directory, but **do not modify existing context files until the user approves the proposed change plan**. Keep current public APIs compatible; add individual hooks without removing any established aggregate hook. Preserve the existing context implementation library unless migration between native React context and `use-context-selector` is explicitly requested and approved.

## Execution workflow

1. **Inspect before implementing.** Locate the relevant app/package (also in a monorepo), read its `package.json`, code layout, relevant contexts, imports/consumers, language settings, TypeScript config, alias configuration, ESLint/formatting rules, and applicable repo instructions. Identify Next.js and its App Router/Pages Router structure. Check whether **the relevant package directly declares** `use-context-selector` in dependencies (or otherwise has it explicitly available); do not assume a transitive dependency is usable.
2. **Choose the context implementation.** For a new context, use `createContext` and `useContextSelector` from `use-context-selector` when that library is available in the relevant package; otherwise use `createContext` and `useContext` from `react`. For an existing context, keep its original implementation unless expressly asked to switch. Never install a library or modify a manifest/lockfile just for this skill.
3. **Translate natural language into a context contract.** Infer a descriptive name, domain types, safe initial state, actions and arguments, optional derived values, and named individual hooks. Reuse existing domain models and services; do not invent backend API endpoints, credentials, or contracts. Prefer `useState`; choose `useReducer` only if several related transitions or invariants justify it. Avoid duplicate derived state. Use `useMemo` and `useCallback` only where useful for correctness, stable public callbacks, or rendering performance; do not apply them mechanically. Ask a focused question only when a safe inference is impossible.
4. **Choose operation mode.**
   - **Create:** If the path does not exist and no other approval is needed, create the new context directly. If there is a naming/path collision, stop and resolve it rather than overwrite.
   - **Adapt/modify:** First show a short plan specifying target paths, original exports/contracts, files and consumers that may need edits, compatibility impacts, and the intended library. **Wait for explicit approval before changing any existing context file.** A request to modify a context is not itself approval to skip the proposed-plan checkpoint.
   - **Provider integration:** Do **not** insert a Provider into the component tree without a separate confirmation. Propose the nearest common ancestor of its consumers. Modifying any file **outside the context directory** also requires advance disclosure and explicit scope approval, even when context-file changes were approved.
5. **Implement minimally.** Follow the original import aliases, style and project conventions. Keep existing names, parameter/return types, behavior, and consumers compatible. Add new hooks individually in `hooks.ts`, preserve old imports until approved to update them, and avoid unrelated refactors. Do not change an existing native React context into `use-context-selector` merely because the dependency exists.
6. **Validate and report.** Run existing typecheck and lint commands **when available and feasible**, without adding dependencies or running tests. Prefer scoped lint; do not run repo-wide auto-fixes. Distinguish pre-existing failures from newly introduced errors. Fix errors caused by your changes; if impossible, roll back **only your own edits**, protecting the user's pre-existing/uncommitted changes. Report the created/changed files, decisions, validation results, and any remaining limitations.

## Context implementation requirements

- For **new TypeScript contexts**, retain the user's explicit default-value pattern: `createContext({} as ContextProps)`; import the factory from the selected library. Keep the interface in `index.ts` and use existing shared types when available. For **JavaScript**, use valid plain-JS syntax (`createContext({})`) without TypeScript assertions or interfaces.
- For **native React** `hooks.ts`, use `useContext(Context)` in each independently exported hook, following the user's existing `if (!context) { throw new Error(...) }` style where appropriate. **Limitation:** with the chosen `{}` default, this guard cannot detect a missing provider; it is not a runtime guarantee. Do not silently switch to a nullable context to fix this; flag the caveat when relevant.
- For **`use-context-selector`**, import both `createContext` and `useContextSelector` from that library; select a single property with each hook and prefer selectors that return referentially stable values. Avoid mixing native `useContext` with a selector-library context.
- Use `'use client'` **only in Next.js contexts that require a client boundary**, not in Vite/CRA/other conventional React apps or merely because a file has `.tsx` extension. In the Next.js App Router, follow actual server/client import boundaries; it is not always necessary to put the directive in all three files. Never move browser-only stateful code into a Server Component.
- Follow the Rules of Hooks. Keep reducer transitions pure, use functional updates where required, prevent stale closures, and preserve immutability. For asynchronous actions, use existing services, handle loading/error state only if needed, and prevent known unmount/race problems where relevant.
- Do **not** create tests. Do **not** add a one-hook-for-the-whole-context API to new contexts. When adapting an existing context, retain existing aggregate hooks as compatibility shims unless explicitly approved to remove them.
- Never use destructive Git commands (`git reset --hard`, `git checkout -- .`, etc.), overwrite unrelated changes, or perform unrequested refactors.

## Read references only as needed

- Native React Context patterns and caveats: [references/react-context-api.md](references/react-context-api.md)
- Selector-based contexts: [references/use-context-selector.md](references/use-context-selector.md)
- Next.js boundaries and Provider placement: [references/nextjs-integration.md](references/nextjs-integration.md)
- TypeScript/JavaScript and naming: [references/javascript-typescript.md](references/javascript-typescript.md)
- Approvals, adaptation, consumer compatibility and rollback: [references/context-modification.md](references/context-modification.md)
- Example requirements-to-files mappings and more complex scenarios: [references/examples.md](references/examples.md)
