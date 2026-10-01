# React Component Formatting Rules

Load this reference completely before creating, formatting, or refactoring React components with `$format-react-component`.

## Priorities

Apply these priorities in order:

1. Preserve runtime behavior and public contracts.
2. Preserve the correct framework, router, and Server/Client boundary.
3. Follow explicit project tooling and established local conventions.
4. Make only safe, local, reviewable improvements.
5. Improve structural consistency and readability.

When priorities conflict, keep the safer code in place and report the reason. Do not broaden the task to resolve an unrelated architectural issue.

## Inspect the Project

Inspect the target, its callers, sibling components, tests, and at least the applicable files below:

```text
package.json
tsconfig.json
jsconfig.json
next.config.*
vite.config.*
eslint.config.*
.eslintrc*
prettier.config.*
.prettierrc*
biome.json
```

Also inspect directory layout, path aliases, import patterns, file naming, component declaration style, prop typing, exports, semicolons, quotes, indentation, CSS conventions, colocated hooks, and project scripts.

Use dependencies and active configuration together. The presence of an old or unused package alone does not prove that a framework is active.

## Detect the Environment

### Plain React

Treat the project as plain React when React is active and Next.js is not the active framework. Common evidence includes `react`, `react-dom`, Vite, Webpack, Parcel, or `react-scripts`.

In plain React:

- ignore Next.js Server Component and router rules;
- never add `"use client"`;
- do not introduce Next.js imports or conventions;
- preserve React Router or the existing routing solution.

### Next.js routers

Detect router presence from structure:

- `app/` or `src/app/`: App Router is present.
- `pages/` or `src/pages/`: Pages Router is present.
- both structures: the project is hybrid; determine context from the target path, imports, callers, and route ownership.

Never infer App Router merely from the Next.js version. Never migrate APIs because another router also exists.

### JavaScript and TypeScript

Use the target extension, nearby files, TypeScript configuration, and project convention. Preserve `.js`/`.jsx` or `.ts`/`.tsx` unless conversion was explicitly requested. Do not add types to JavaScript files as part of formatting.

## Router-Specific Files

### App Router

Recognize these special files and preserve their framework contracts:

```text
page.tsx        page.jsx
layout.tsx      layout.jsx
loading.tsx     loading.jsx
error.tsx       error.jsx
not-found.tsx   not-found.jsx
template.tsx    template.jsx
default.tsx     default.jsx
route.ts        route.js
```

Do not treat route handlers as React components. Preserve required exports and signatures. `error` files and other framework-defined client entry points may have requirements beyond code visible in the file; verify current local framework behavior before changing their directive.

Use `next/navigation` only in the App Router context. Recognize `useRouter`, `usePathname`, `useSearchParams`, and `useParams` as client hooks.

Keep pages and layouts focused on composition when that matches the existing architecture. Extract complex UI only when the change is local, clearly safe, and useful to the request.

### Pages Router

Recognize route files, dynamic segments, `_app`, and `_document`. Preserve Next.js data-fetching exports and special component contracts.

Use and preserve `next/router` in Pages Router code. Never replace it with `next/navigation` as a formatting operation.

## Determine the Server/Client Boundary

In App Router, keep a component on the server unless client execution is required.

Client execution is normally required by:

- client-only React hooks such as `useState`, `useReducer`, `useEffect`, `useLayoutEffect`, `useRef`, `useImperativeHandle`, `useTransition`, `useDeferredValue`, or `useSyncExternalStore`;
- interactive event handlers such as `onClick`, `onChange`, `onSubmit`, `onKeyDown`, `onInput`, `onBlur`, or `onFocus` on rendered elements;
- browser APIs such as `window`, `document`, `localStorage`, `sessionStorage`, or `navigator`;
- an imported dependency whose documented runtime requires a Client Component.

Do not add `"use client"` merely because a component:

- is async;
- fetches data on the server;
- renders a Client Component child;
- receives serializable props;
- appears under another Client Component in one usage site.

Before marking a large component as client-rendered:

1. Identify the exact client-only statements and imports.
2. Check whether an existing Client Component already owns them.
3. Consider extracting only the interactive region when the boundary and props remain simple and serializable.
4. Keep the existing boundary and report the opportunity when extraction would create broad file movement, new architecture, risky state transfer, or unclear rendering changes.

Do not remove an existing directive until all imports, hooks, browser usage, rendered handlers, dependencies, callers, and framework requirements have been checked.

## Imports

First detect configured ordering from `eslint-plugin-simple-import-sort`, `eslint-plugin-import`, `@trivago/prettier-plugin-sort-imports`, Biome, or another explicit tool. Follow that tool and do not create a competing order.

Without configured ordering, prefer:

1. React
2. framework
3. external libraries
4. internal absolute aliases
5. relative imports
6. styles and assets
7. type-only imports according to project convention

Preserve side-effect imports, import attributes, comments, aliases, type-only semantics, paths, and module boundaries. Do not merge, remove, or reorder imports when comments or evaluation order may be affected. Let lint or the compiler identify unused imports unless their removal is unambiguously in scope and behavior-neutral.

Directives must remain before imports. In files that require it, keep `"use client"` as a directive prologue rather than moving it into an ordinary statement group.

## Props, Types, and Exports

For non-trivial TypeScript props, prefer a named prop type when compatible with local conventions. Preserve the project's choice between `type` and `interface`; never convert between them for aesthetics.

Preserve generics, overloads, `satisfies`, type predicates, assertions, discriminated unions, `readonly`, optionality, defaults, and component call signatures.

Preserve named versus default exports when valid. Respect exports required by Next.js special files. Do not change export style globally or rename public symbols as part of formatting.

## Safe Internal Order

Within a component, prefer this hook order when movement is demonstrably safe:

1. framework and router hooks
2. context hooks
3. store and state-management hooks
4. data-fetching and custom hooks
5. `useState` and `useReducer`
6. `useRef`
7. justified `useMemo`
8. justified `useCallback`
9. `useEffect`
10. `useLayoutEffect` and specialized effects

Then place event handlers, helper functions, early returns, and the JSX return.

This is a structural preference, not permission to change evaluation order. Before moving a declaration, inspect every value read during initialization and every side effect. Preserve relative order for hooks, subscriptions, registrations, timers, imperative calls, mutations, data requests, and dependent declarations.

Never call a hook conditionally, inside a loop, inside an ordinary callback, after an early return that can skip it, or from a function that is not a React component or custom hook. Do not reorganize hooks solely for visual grouping when safety is uncertain.

Keep comments attached to the declarations or JSX they describe. Preserve JSDoc, TODO, FIXME, ESLint, Prettier, TypeScript, framework, and business comments.

## Control Flow and Callbacks

Always use braces for control-flow bodies. Expand conditional returns to blocks, including `return`, `return null`, JSX returns, thrown errors, and early exits.

Use parentheses around every single arrow parameter:

```tsx
const names = users.map((user) => {
  return user.name
})
```

Use explicit blocks for callbacks that return values in `map`, `filter`, `find`, `findIndex`, `some`, `every`, `flatMap`, `reduce`, `sort`, Promise chains, and equivalent transformations. Apply a block body to `forEach` and event transformation callbacks as well; do not add a value return where the API does not use one.

When converting a callback body, preserve expression evaluation, object-literal returns, async behavior, types, comments, and error propagation. Do not convert concise arrows when the transformation could alter TypeScript inference or behavior; report the conflict instead.

## Derived Values, Memoization, and Effects

Prefer direct derivation during render when it is semantically equivalent:

```tsx
const fullName = `${firstName} ${lastName}`
```

Do not introduce `useMemo` for trivial calculations. Use memoization only for an identifiable need such as material computation cost, required referential identity, a dependency contract, or a memoized consumer that benefits from stability.

Do not introduce `useCallback` merely to make handlers appear organized. Use it only when stable identity has an observable technical purpose.

Treat effects as synchronization with systems outside ordinary rendering. Identify state that could be derived directly, but do not remove an existing effect when timing, subscriptions, cleanup, external mutation, or behavior is uncertain.

Preserve dependency arrays unless a correction is clearly required by the task and can be validated. Never silence hook lint rules or change configuration to permit questionable dependencies.

## Event Handlers and Helpers

Prefer named handlers when JSX contains multi-step logic or when a name clarifies intent. Keep short inline callbacks when they are locally clear and consistent with the project, but still apply braces and parenthesized parameters.

Do not convert an arrow to a function declaration when it depends on lexical `this`, identity, initialization order, generic inference, overloads, attached properties, reassignment, or another semantic distinction.

Place pure module-level utilities outside the component only when they do not capture component state and the move is local and safe. Do not create utility files merely to reduce component length.

## Custom Hooks and Large Components

Custom hooks must start with `use`, follow the Rules of Hooks, have a coherent responsibility, and preserve the component's behavior. Do not create a hook solely to reduce line count or to hide mixed domain, UI, and infrastructure logic.

For a large component, identify possible child components, hooks, utilities, schemas, types, or constants. Implement an extraction only when it is local, easy to validate, compatible with established patterns, and does not alter public contracts or architecture. Otherwise report it as an opportunity.

## Tooling Conflicts

Treat ESLint, Prettier, Biome, TypeScript, import sorters, and project scripts as sources of truth. Do not modify their configuration merely to make the target pass.

The skill's mandatory conventions are:

- braces for all control-flow bodies;
- no same-line conditional return;
- no implicit-return callbacks;
- parentheses around every single arrow parameter.

If a formatter directly conflicts with one of these conventions, leave configuration unchanged, apply only stable non-conflicting edits, and report the conflict. Do not create repeated formatter churn.

## Forbidden Automatic Changes

Do not implicitly:

- migrate App Router to Pages Router or Pages Router to App Router;
- replace React Router or another router;
- replace state-management, data-fetching, validation, or styling systems;
- mass-convert JavaScript to TypeScript;
- convert all named exports to default exports or the reverse;
- change prop, request, authorization, rendering, or data contracts;
- introduce Context API, Redux, Zustand, Jotai, or another state system;
- add speculative memoization;
- add `"use client"` for convenience;
- remove `"use client"` without complete runtime analysis;
- switch between `next/router` and `next/navigation`;
- move large sections into new files without a clear, validated local need;
- alter hook semantics, effect timing, or rendering behavior.

## Validation

Inspect `package.json` and use the existing package manager and scripts. Prefer focused checks, expanding only as risk requires:

1. Run the configured formatter in check mode or against the target when available. Do not run a write-mode formatter over unrelated files.
2. Run lint on the target or relevant package.
3. Run TypeScript or the project's typecheck when applicable.
4. Run relevant unit or integration tests.
5. Run a Next.js or application build when router, Server/Client boundary, special-file contracts, or compilation behavior cannot be validated more narrowly.
6. Inspect the final diff for behavior changes and unrelated edits.
7. Check idempotence when practical by confirming that the same rules produce no further structural changes.

Do not claim a command passed unless it was executed. If a command is unavailable, too broad, or fails for a pre-existing reason, state that precisely.

Confirm all of these invariants:

```text
[ ] Correct React/Next.js environment detected
[ ] Correct target router detected
[ ] Server/Client boundary preserved or safely narrowed
[ ] "use client" exists only where justified
[ ] Hook order and Rules of Hooks remain valid
[ ] No hook was moved after a possible early return
[ ] Imports follow configured or local conventions
[ ] Props, types, comments, and exports preserve contracts
[ ] Existing behavior is preserved
[ ] No unnecessary memoization, effect, hook, or abstraction was added
[ ] No architectural migration occurred implicitly
[ ] All control-flow bodies use braces
[ ] No same-line conditional return exists
[ ] No implicit-return callback was introduced
[ ] Every single arrow parameter is parenthesized
[ ] Relevant validation commands passed or limitations were reported
[ ] Reapplying the skill would produce no unnecessary changes
```

## Reporting

Use concise outcomes such as:

```text
Created: src/components/UserCard.tsx
Formatted: src/components/UserCard.tsx
Refactored with warnings: src/components/UserCard.tsx
Unchanged: src/components/UserCard.tsx
Skipped unsafe extraction: src/components/UserCard.tsx
```

Include the detected environment and router when relevant, validation commands and results, formatter conflicts, assumptions made for snippet-only input, declarations kept in place because of dependencies, and architectural opportunities intentionally not implemented.
