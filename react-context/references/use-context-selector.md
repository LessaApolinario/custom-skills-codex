# `use-context-selector` reference

Read this when the relevant package explicitly has `use-context-selector` available, or when a user requests a migration to it.

## Exact library pairing

Import **both** `createContext` and `useContextSelector` from `use-context-selector`. Native React `createContext` is not interchangeable in this pattern. Do not add the dependency if absent. For existing contexts, retain the existing library unless an explicit, approved migration is requested.

### `index.ts`

```tsx
import { createContext } from 'use-context-selector'
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
import { useContextSelector } from 'use-context-selector'
import { ActiveScreenContext } from '.'

export function useActiveScreen() {
  return useContextSelector(ActiveScreenContext, (context) => context.activeScreen)
}

export function useNavigateToScreen() {
  return useContextSelector(ActiveScreenContext, (context) => context.navigateToScreen)
}
```

## Selector considerations

- Each hook should select **exactly the needed public value or action**, as in the example. Avoid returning a new object/array from a selector on each invocation; selector results should remain referentially stable for unchanged selections.
- Make shared action references stable when useful (`useCallback`) and consider stabilizing the provider's `value` when measurements or usage patterns justify it.
- Do not promise zero re-renders. Consult the library's documented limitations; parent re-renders, props, and concurrent behavior may affect consumers independently of context selection.
- The chosen `{}` default also does not protect against missing Provider usage; a selector may produce `undefined` in that situation. Do not assert otherwise.
- Do not silently replace the selector library's factory with React's `createContext`, or vice versa.
- For JavaScript, remove TypeScript type declarations and assertions, preserving the same context/provider/hooks split.
- `useContextSelector` hooks may live in `hooks.ts`, with an appropriate Next.js client boundary only where required.

Reference: https://github.com/dai-shi/use-context-selector
