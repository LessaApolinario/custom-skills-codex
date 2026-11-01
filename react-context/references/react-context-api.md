# Native React Context API reference

Read this when `use-context-selector` is not available in the target package or when updating an existing native React context.

## Required TypeScript layout

For this example, place the files in a `active-screen/` directory. `ActiveScreen` is a domain type that must already exist or be defined where the repository normally keeps shared types. Match import paths, semicolon rules and quote style to the project.

### `index.ts`

```tsx
import { createContext } from 'react'
import type { ActiveScreen } from '@/lib/@types/ActiveScreen'

interface ActiveScreenProps {
  activeScreen: ActiveScreen
  navigateToScreen: (screen: ActiveScreen) => void
}

export const ActiveScreenContext = createContext({} as ActiveScreenProps)
```

### `ActiveScreenProvider.tsx`

```tsx
import { useCallback, useState } from 'react'
import type { PropsWithChildren } from 'react'
import type { ActiveScreen } from '@/lib/@types/ActiveScreen'
import { ActiveScreenContext } from '.'

export function ActiveScreenProvider({ children }: PropsWithChildren) {
  const [activeScreen, setActiveScreen] = useState<ActiveScreen>('home')

  const navigateToScreen = useCallback((newScreen: ActiveScreen) => {
    setActiveScreen(newScreen)
  }, [])

  return (
    <ActiveScreenContext.Provider value={{ activeScreen, navigateToScreen }}>
      {children}
    </ActiveScreenContext.Provider>
  )
}
```

### `hooks.ts`

```tsx
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

**Important limitation:** `createContext({} as ActiveScreenProps)` passes a truthy `{}` as the default value. If a hook runs without a Provider, `if (!context)` will not throw. The selected convention must still be preserved. Do not claim the guard protects against missing Providers; report the caveat when it matters. Do not silently replace the default with `null`.

## Rendering and state management

- In native React Context, `useContext` subscribes to the context as a whole; separate hooks do not provide selector-level render isolation. Do not make performance claims to the contrary.
- Include a stable action function with `useCallback` when it improves identity consistency or prevents downstream effects. Use `useMemo` for the provider value when justified, rather than mechanically.
- Use functional `setState` updates when the next state depends on the previous value.
- Use `useReducer` only for state machines or interconnected updates with real invariants; its `dispatch` can be wrapped with semantic functions if the desired public API consists of actions, not raw dispatch.
- If an existing context exports `useActiveScreenContext()` returning all values, keep it during adaptation and add the smaller hooks. Removing it is a breaking change and needs explicit approval.
- Prefer existing repository conventions over the example's exact formatting. In Next.js App Router, client boundaries are covered separately in `nextjs-integration.md`.

Reference: https://react.dev/reference/react/createContext
