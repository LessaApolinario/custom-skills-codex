# Next.js detection, client boundaries, and Provider integration

Read this when the target package uses Next.js or the user asks where a context Provider should be mounted.

## Detection

1. Confirm that the relevant package (not merely an unrelated workspace root) depends on `next`. Check package manifests, framework configuration, existing routing folders, and actual imports.
2. Identify App Router (`app/`, `src/app/`), Pages Router (`pages/`, `src/pages/`), or a mixed project. Do not infer the router from the presence of Next.js alone.
3. Identify whether the new or existing Provider/hook is imported from a Server Component, an established Client Component, or another shared module.

## `'use client'` policy

- **App Router:** Hooks and stateful providers must operate on the client. Put `'use client'` at the top of a file that needs to establish a client module entry point, before imports. A Provider exported directly for use within a Server Component (such as a server layout) should be a Client Component entry point.
- A file imported **only inside an existing client graph** does not need its own redundant directive. In particular, do not automatically prefix all three context files: evaluate actual import/boundary requirements. A hooks entry file can be marked client when it needs to be an explicit boundary or when the surrounding project convention warrants it.
- `index.ts` may hold the `createContext` declaration and can be imported from a client-boundary module. Avoid importing or using client-only context APIs directly in Server Components. If `index.ts` itself must be a client entry point, add the directive there.
- **Pages Router:** The client/server component module boundary convention of the App Router does not apply; do not add `'use client'` just because the app uses Next.js.
- **Non-Next React:** Never add the directive.
- Preserve existing top-of-file directives and follow exact quote/semicolon conventions from the repository.

## Provider integration requires separate approval

1. Determine which components actually consume the context and their closest common ancestor.
2. Recommend that ancestor, balancing state lifetime, required scope, and Next.js client boundaries; do not place every Provider at the application root by default.
3. Explain which files would be edited and whether the wrapping will change component identity/state lifecycle.
4. Ask for explicit approval before integrating the Provider, **even after approval to create or adapt the context**.
5. Ask for separate scope approval for consumer/layout changes outside the context directory. Do not silently edit them.

### Example: App Router boundary (only after approval)

`app/providers.tsx`:

```tsx
'use client'

import type { PropsWithChildren } from 'react'
import { ActiveScreenProvider } from '@/contexts/active-screen/ActiveScreenProvider'

export function Providers({ children }: PropsWithChildren) {
  return <ActiveScreenProvider>{children}</ActiveScreenProvider>
}
```

A server `app/layout.tsx` may import this client entry point and render `<Providers>{children}</Providers>` without converting the whole layout module into a Client Component. Use the existing app's actual path and architecture. If a nearer client ancestor exists, prefer that instead.

### Common pitfalls

- Do not mark a Server Component as client only to create a globally wrapping Provider when a smaller client boundary will suffice.
- Do not pass non-serializable functions from a Server Component as props to a new client boundary. Define context actions in the Client Provider or use an existing supported client-side service.
- Do not treat the existence of `next` in a monorepo root as proof that all workspace packages use Next.js.
- Do not introduce `'use client'` into Pages Router or plain React projects gratuitously.

References:
- https://nextjs.org/docs/app/api-reference/directives/use-client
- https://nextjs.org/docs/app/getting-started/server-and-client-components
