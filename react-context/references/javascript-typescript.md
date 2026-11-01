# JavaScript, TypeScript, and naming conventions

Read this when choosing file extensions, generating context contracts, or deciding what may be inferred safely.

## Project-first detection

- Inspect `tsconfig.json`/`jsconfig.json`, current context/component files, React tooling and existing conventions. Prefer the language used by the local package and surrounding code. Do not turn a JavaScript project into TypeScript or create a TypeScript configuration as part of this skill.
- Respect module format, JSX conventions, import style (including configured aliases), filename casing, indentation, semicolons, quote style, and import grouping. Treat code samples as structural patterns, not formatting mandates.
- Import known domain types from their current home. Do not duplicate existing types or introduce a new `types.ts` for ordinary cases. Keep the TypeScript interface describing context value in `index.ts`.

## Naming rules

| Item | Required pattern | Example |
| --- | --- | --- |
| Context directory | kebab-case | `active-screen/` |
| Provider | PascalCase | `ActiveScreenProvider` |
| Provider filename | PascalCase | `ActiveScreenProvider.tsx` or `.jsx` |
| Context | PascalCase | `ActiveScreenContext` |
| Contract | PascalCase, suffixed `Props` by default | `ActiveScreenProps` |
| State | camelCase | `activeScreen` |
| Semantic action | camelCase verb | `navigateToScreen` |
| Individual hook | `use` + PascalCase | `useActiveScreen`, `useNavigateToScreen` |
| Hook file | fixed | `hooks.ts` or `.js` |
| Context factory file | fixed | `index.ts` or `.js` |

Respect established public identifiers during adaptation even if their names differ from the suggested patterns. Rename only with explicit breaking-change approval.

## TypeScript contract style

For new TypeScript contexts, keep the user-chosen default pattern:

```tsx
import { createContext } from 'react'
import type { ActiveScreen } from '@/lib/@types/ActiveScreen'

interface ActiveScreenProps {
  activeScreen: ActiveScreen
  navigateToScreen: (screen: ActiveScreen) => void
}

export const ActiveScreenContext = createContext({} as ActiveScreenProps)
```

For `use-context-selector`, switch only the factory import. Do not force `null` or `undefined` as the default context value. A state *property* can still use `null` when genuinely part of the domain (e.g. `selectedUser: User | null`).

- Prefer explicit domain types and action signatures. Use `import type` when supported and consistent with the project.
- For callback arguments and reducer actions, avoid `any` when a precise type can be safely inferred.
- For new `useReducer` implementations, keep action types narrow and transitions exhaustive; additional files are justified only if complexity merits them.

## JavaScript equivalent

For a JavaScript React project, use `active-screen/ActiveScreenProvider.jsx`, `active-screen/hooks.js`, `active-screen/index.js`.

`index.js`:

```jsx
import { createContext } from 'react'

export const ActiveScreenContext = createContext({})
```

`ActiveScreenProvider.jsx`:

```jsx
import { useCallback, useState } from 'react'
import { ActiveScreenContext } from '.'

export function ActiveScreenProvider({ children }) {
  const [activeScreen, setActiveScreen] = useState('home')

  const navigateToScreen = useCallback((newScreen) => {
    setActiveScreen(newScreen)
  }, [])

  return (
    <ActiveScreenContext.Provider value={{ activeScreen, navigateToScreen }}>
      {children}
    </ActiveScreenContext.Provider>
  )
}
```

`hooks.js`:

```jsx
import { useContext } from 'react'
import { ActiveScreenContext } from '.'

export function useActiveScreen() {
  const context = useContext(ActiveScreenContext)

  if (!context) {
    throw new Error('useActiveScreen must be used within an ActiveScreenProvider')
  }

  return context.activeScreen
}

export function useNavigateToScreen() {
  const context = useContext(ActiveScreenContext)

  if (!context) {
    throw new Error('useNavigateToScreen must be used within an ActiveScreenProvider')
  }

  return context.navigateToScreen
}
```

The same caveat applies in JavaScript: `{}` is truthy, so the guard does not detect a missing Provider. Do not claim otherwise.

## Safe inference guidelines

- A boolean such as `isOpen` can usually begin as `false`; a list can usually begin as `[]`; a counter can often begin as `0`.
- A named enum or union may have a conventional initial value only when clearly supported by existing code/domain requirements. Do not assume an arbitrary screen, status, access role, or authenticated identity.
- A nullable domain value is valid when absence is a real state, e.g. `selectedItem: Item | null` initialized as `null`.
- When a default affects domain behavior, persistence, authorization, pricing, or other non-trivial invariants, ask instead of guessing.
- Never add a generic `useEntireContext` hook to new contexts simply for convenience. Individual hooks are the public API.
