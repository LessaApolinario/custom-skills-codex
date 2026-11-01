# Example interpretations and decision scenarios

Use these as behavioral examples, not rigid output templates. Always inspect the real project and follow its conventions.

## Example 1: New context from a short description

**Request:** "Create a context to store the active screen and navigate to another screen."

**Preflight:** Identify the active-screen domain type and its valid initial value. Check if `use-context-selector` is directly available in the relevant package. Detect Next.js and routing mode; choose the correct client boundary only if required.

**Deliver:**

```text
contexts/active-screen/
  ActiveScreenProvider.tsx
  hooks.ts
  index.ts
```

Contract:

```text
State:  activeScreen: ActiveScreen
Action: navigateToScreen(screen: ActiveScreen): void
Hooks:  useActiveScreen(), useNavigateToScreen()
```

Create directly if there are no existing files/path conflicts. Do **not** mount the Provider without separate approval. See `react-context-api.md` and `use-context-selector.md` for exact three-file implementations.

## Example 2: Adapt a legacy single-file context

**Request:** "Convert our existing AuthContext into the standard react-context architecture."

**Found:** `src/context/AuthContext.tsx` exports `AuthProvider`, `useAuth`, and actions `login`/`logout`; several consumer imports target the original file.

**Proposed result:**

```text
src/contexts/auth/
  AuthProvider.tsx
  hooks.ts
  index.ts
```

- Preserve existing implementation library, `AuthProvider`, `useAuth`, `login`, `logout`, state values, and signatures.
- Add focused hooks such as `useUser()`, `useLogin()`, `useLogout()` to `hooks.ts`.
- Explain any old-path forwarding module and imports that need updating. This is an outside-directory edit and requires separate scope approval.
- Present a concrete proposal and **wait for approval before changing the existing context**. Do not unilaterally migrate native Context to `use-context-selector`.
- If users want the aggregate `useAuth()` removed, obtain explicit breaking-change approval and an agreed consumer migration.

## Example 3: Feature addition to an existing standard context

**Request:** "Add a way to return to the previous screen."

- Inspect `active-screen/` and its consumers. Determine whether history should be a stack or whether a single previous screen suffices; ask only when the choice materially changes navigation behavior and cannot be inferred.
- Propose additions (`previousScreens`, `navigateBack`, `useNavigateBack`) and necessary updates to the interface, Provider, and hooks.
- Preserve existing `useActiveScreen()` and `useNavigateToScreen()` contracts.
- Obtain approval first. Do not introduce unrelated changes.

## Example 4: Complex cart state

**Request:** "Create a cart context that can add items, remove items, adjust quantities, and clear all items. Expose the total price."

Possible contract:

```text
State:   items: CartItem[]
Actions: addItem(item: CartItem), removeItem(id: string),
         updateQuantity(id: string, quantity: number), clearCart()
Derived: totalPrice: number
Hooks:   useCartItems(), useAddCartItem(), useRemoveCartItem(),
         useUpdateCartItemQuantity(), useClearCart(), useCartTotalPrice()
```

- Prefer `useReducer` if quantity transitions, deduplication and stock invariants interact; simple cases can remain `useState`.
- Use a derived `totalPrice` value instead of storing a separately mutable total. Memoization may be appropriate for a large list or expensive computation.
- **Do not invent** tax rules, discounts, currency conversions, or product price fields that do not exist in the domain. If they are required, ask.
- Use a reducer helper file only when justified and keep the three required files.
- Expose semantic actions, not raw reducer dispatch.

## Example 5: Existing async service

**Request:** "Create a context to load customer details from our existing customer service."

- Locate the service and its actual function signatures and return types before writing code.
- Determine whether `customer`, `loading`, `error`, and `loadCustomer(id)` are part of the requested public API; only expose what is useful. A plausible hook set is `useCustomer()`, `useCustomerLoading()`, `useCustomerError()`, `useLoadCustomer()`.
- Avoid unrequested network libraries, endpoints and auth assumptions. Handle stale results or unmount cases when the flow needs it.
- Keep failures observable using existing conventions and preserve public return types.

## Example 6: JavaScript + Vite, no selector library

**Request:** "Create a modal context to open, close, and toggle the modal."

Expected directory:

```text
src/contexts/modal/
  ModalProvider.jsx
  hooks.js
  index.js
```

Use `createContext({})` and `useContext` from React, no TypeScript syntax, and **no `'use client'` directive**. Prefer `isOpen: false` where consistent with the project. Export individual hooks, e.g. `useIsModalOpen()`, `useOpenModal()`, `useCloseModal()`, `useToggleModal()`.

## Example 7: Next.js App Router Provider placement

**Request:** "Create a theme context for our dashboard."

- Create the three files, applying client boundaries where necessary.
- Identify dashboard components that consume the context and recommend the closest shared ancestor instead of always using `app/layout.tsx`.
- Explain the proposed wrapping location and **ask before modifying the tree**.
- Changes to `app/layout.tsx`, `app/dashboard/layout.tsx`, or an existing `Providers` composition file require explicit approval for those external files.

## Final implementation checklist

- [ ] Context is placed in a kebab-case directory at the project's established location.
- [ ] Exactly the three core files exist for a new context, with extras only when justified.
- [ ] Correct native/selector imports; no dependency installed.
- [ ] No redundant `'use client'` outside required Next.js client boundaries.
- [ ] Interface is in `index.ts` when using TypeScript; no TypeScript syntax in JavaScript.
- [ ] New public API uses separate hooks and semantic action names.
- [ ] Existing contracts and consumer import paths remain supported, or an approved migration handles them.
- [ ] Changes to existing context files, any outside-directory files, and Provider integration have their respective approvals.
- [ ] Existing typecheck/lint run when feasible; no tests generated or executed.
- [ ] Any introduced failures are fixed or only the skill's edits are safely reverted.
- [ ] Final response summarizes changes and limitations without claiming unperformed validation.
