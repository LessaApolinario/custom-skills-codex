---
name: format-react-component
description: Create, organize, format, and safely refactor React and Next.js components in JavaScript or TypeScript. Use for new or existing components, related custom hooks, App Router or Pages Router code, Server/Client Component decisions, and local behavior-preserving cleanup. Do not use it to migrate routers or replace project architecture implicitly.
---

# Format React Component

## Core Rule

Preserve behavior and the project's valid conventions before applying structural preferences. Analyze aggressively and modify conservatively. Keep every transformation local, reviewable, and idempotent; a second run must not create formatting churn.

Before creating or changing any React component, read [references/react-component-formatting-rules.md](references/react-component-formatting-rules.md) completely.

## Determine the Mode

Infer the mode from the request and repository state; do not require the user to name it.

- **CREATE:** Create a missing component according to the detected architecture and nearby conventions.
- **FORMAT:** Normalize a valid existing component without changing behavior.
- **REFACTOR:** Correct safe local React or structural problems. Report broad architectural opportunities instead of silently implementing them.

## Required Workflow

When repository access is available, follow this sequence:

1. Inspect the project and the target's callers, siblings, and tests.
2. Detect React or Next.js from dependencies, configuration, scripts, and directory structure.
3. For Next.js, detect App Router, Pages Router, or both, then determine the target's actual context.
4. Detect JavaScript or TypeScript and the target file convention.
5. Inspect nearby components and project conventions.
6. Inspect ESLint, Prettier, Biome, TypeScript, import-sorting, test, and build configuration.
7. In App Router, determine whether the target must be a Client Component; otherwise preserve it as a Server Component.
8. Analyze imports, hooks, initialization dependencies, side effects, early returns, component structure, exports, and comments.
9. Apply only safe creation, formatting, or local refactoring.
10. Validate with the narrowest relevant project commands, then check the result for idempotence.

Do not skip project inspection when the repository is available. If only a code snippet is available, state the assumptions that materially affect the result.

## Architecture Boundaries

- Treat React without an active Next.js framework as plain React. Do not add `"use client"` or Next.js APIs.
- Detect App Router from `app/` or `src/app/` and Pages Router from `pages/` or `src/pages/`. If both exist, use target location and usage; never assume the whole project uses one router.
- Treat App Router components as Server Components by default. Add `"use client"` only when client-only hooks, interactive event handlers, browser APIs, or a required dependency make client execution necessary.
- Before expanding a client boundary, check whether a small interactive child can be extracted safely. Do not perform a broad extraction merely to avoid a directive.
- Preserve `next/router` in Pages Router and `next/navigation` in App Router. Switching them is an architectural migration.
- Handle App Router special files and Pages Router special files according to their framework contracts rather than as generic components.
- Preserve valid export style, routing, state management, data fetching, styling, authorization, request, rendering, and prop contracts.

Never introduce `useMemo`, `useCallback`, `useEffect`, a custom hook, context, or another abstraction merely to match an ordering preference or reduce line count.

## Component Structure

Use this order only when it is safe and does not conflict with project tooling:

1. directives
2. imports
3. types and interfaces
4. module-level constants
5. component declaration
6. props destructuring
7. framework/router hooks
8. context hooks
9. store hooks
10. data-fetching and custom hooks
11. local state and reducers
12. refs
13. derived values
14. justified memoized values and callbacks
15. effects
16. event handlers and helpers
17. early returns
18. JSX return
19. small local auxiliary components
20. exports not declared inline

Never move a hook across a conditional, loop, callback, or early return. Never reorder declarations when initialization dependencies, effect order, or observable behavior may change.

Treat configured import sorters as the source of truth. Otherwise prefer React, framework, external packages, internal absolute imports, relative imports, styles/assets, and type-only imports according to local convention.

## Mandatory Syntax Conventions

These conventions apply to code created or formatted by this skill:

- Always use braces for `if`, `else`, `for`, `for...of`, `for...in`, `while`, and `do...while`.
- Never place a conditional return on the same line as its condition.
- Use explicit block bodies for value-returning callbacks, including array and Promise callbacks.
- Always parenthesize a single arrow-function parameter.
- If project formatting conflicts with these conventions, do not change formatter configuration. Report the conflict.

### Early return

Incorrect:

```tsx
if (!user) return null
```

Correct:

```tsx
if (!user) {
  return null
}
```

### Array callback

Incorrect:

```tsx
items.map((item) => item.name)
```

Correct:

```tsx
items.map((item) => {
  return item.name
})
```

### JSX map

Incorrect:

```tsx
{items.map((item) => (
  <Item key={item.id} item={item} />
))}
```

Correct:

```tsx
{items.map((item) => {
  return (
    <Item
      key={item.id}
      item={item}
    />
  )
})}
```

### Single arrow parameter

Incorrect:

```tsx
items.map(item => {
  return item.name
})
```

Correct:

```tsx
items.map((item) => {
  return item.name
})
```

## Server and Client Examples

Keep an async App Router component on the server when it only fetches and renders data:

```tsx
import { UserList } from '@/components/user-list'
import { getUsers } from '@/data/users'

export default async function UsersPage() {
  const users = await getUsers()

  return <UserList users={users} />
}
```

Use a Client Component when client execution is genuinely required:

```tsx
"use client"

import { useState } from 'react'

export function Disclosure() {
  const [open, setOpen] = useState(false)

  function handleToggle() {
    setOpen((currentOpen) => {
      return !currentOpen
    })
  }

  return <button onClick={handleToggle}>{open ? 'Hide' : 'Show'}</button>
}
```

Prefer a narrow client boundary when the extraction is local and safe:

```text
UsersPage (Server Component: fetches data and renders the page)
`-- UserFilters (Client Component: owns input state and event handlers)
```

Preserve Pages Router APIs in Pages Router files:

```tsx
import { useRouter } from 'next/router'

export default function UserPage() {
  const router = useRouter()

  return <p>User: {router.query.id}</p>
}
```

Do not replace this import with `next/navigation` unless the user explicitly requests a router migration.

## Forbidden Implicit Transformations

Do not automatically:

- migrate between App Router and Pages Router;
- replace React Router or another routing solution;
- replace state, fetching, validation, or styling libraries;
- mass-convert JavaScript to TypeScript;
- standardize all exports to named or default exports;
- move large sections into new files without a clear local need;
- change props, requests, authorization, rendering behavior, or hook semantics;
- introduce state libraries, Context API, speculative memoization, or convenience client boundaries;
- remove `"use client"` without checking all imports, descendants, dependencies, and runtime requirements.

## Completion

Finish only after confirming that framework and router detection are correct, the Server/Client boundary is justified, hooks remain unconditional and before relevant early returns, imports/types/exports follow project conventions, behavior is preserved, no implicit migration occurred, mandatory syntax conventions hold, and relevant lint, typecheck, tests, or build checks pass when available.

Report changed and unchanged files, validations run, warnings, skipped unsafe transformations, and any tool conflict. Do not claim validation that was not executed.
