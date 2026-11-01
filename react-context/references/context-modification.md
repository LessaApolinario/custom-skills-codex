# Adapting and modifying existing contexts safely

Read this for any context that already exists, whether it is already in the prescribed format or not.

## Mandatory approval sequence

1. Inspect the existing context files and all discoverable imports/consumers before proposing changes. Include barrel exports, alias paths, mocks and provider composition if present; **do not create tests**.
2. Inventory: context implementation (`react` vs `use-context-selector`), current default value, current Provider, internal state/actions, public hooks, exported names, and usage contracts.
3. Map to the target structure: `kebab-case/`, `PascalCaseProvider.tsx` (or `.jsx`), `hooks.ts` (or `.js`), `index.ts` (or `.js`). Keep existing domain types and behavior. Propose individual hooks for every public state/action while retaining public aggregate hooks if existing consumers still use them.
4. Show the user a concise **change proposal** with target paths, exact old-to-new mapping, preserved exports, required consumer import changes, potential compatibility risks, selected context library, and the planned validation steps. Ask for **explicit approval** before modifying the context.
5. File edits **outside** the context directory (e.g. old file paths, shared barrel files, consumer imports, app layouts) require **separate explicit scope approval**. Provider mounting requires **its own explicit confirmation** even if creating/adapting the context was approved.
6. If approval is absent, stop with the proposal. Do not create half a migration or silently rename directories/files. If complete safe adaptation cannot be done within approved scope, explain the blocker and seek additional approval instead of breaking imports.

## Compatibility preservation

- Keep names, call signatures, returned values, state semantics, error behavior, and observable transitions compatible. Do not rewrite action logic as unrelated refactoring.
- Prefer additive hooks. For example, an existing `useAuth()` may remain supported alongside new `useUser()` and `useLogout()` hooks. Place new individual hooks in `hooks.ts`; preserve the legacy import path if consumers depend on it. A compatibility forwarding module may be appropriate **only after** any outside-directory edits are approved.
- Preserve the existing context library during adaptation. Detecting `use-context-selector` does not authorize migrating an existing native React context. An explicit migration request requires approval and checking consumer behavior.
- Retain working defaults, including existing nullable semantics, if changing them would break behavior. The user's empty-object TypeScript factory pattern is the default for *new* contexts, not a license to break an existing one. If strict structural conformity conflicts with compatibility, present the trade-off and ask.
- If the original context has a name collision with the proposed kebab-case directory or target exports, surface it before touching files.
- Keep existing modified/uncommitted changes intact, including edits unrelated to the requested work. Make only minimal patches and preserve context-specific formatting.
- Do not change public return shapes or remove old aggregate hooks merely because individual hooks are now preferred. Breaking changes require explicit approval and a scoped consumer migration plan.
- Do not switch `useState` to `useReducer` (or vice versa) in an existing context without an approved refactoring scope.

## Proposed change plan template

Use a concise response like:

```text
Proposed context adaptation (approval required)
- Existing implementation: src/context/AuthContext.tsx (native React Context)
- Target: src/contexts/auth/AuthProvider.tsx, hooks.ts, index.ts
- Preserve: AuthProvider, useAuth(), login(), logout(), return types and behavior
- Add: useUser(), useLogin(), useLogout()
- Consumer impact: 4 imports still reference the old module
- Outside-directory edits: compatibility module and 4 consumer files (separate approval)
- Provider integration: not requested; no tree changes
- Validation: existing typecheck and scoped lint
May I apply the context changes? May I also update the listed external files?
```

The example illustrates the content of the approval proposal, not an instruction to invent paths or consumer counts. Use actual repository evidence.

## Error handling and rollback

- Before editing, inspect the working tree and record original content for *the exact files you will change*, including uncommitted user edits. Do not overwrite them or assume a clean Git state.
- After changes, run existing typecheck and lint scripts when feasible. Avoid tests, dependency installation, broad format fixes and destructive Git operations.
- Compare failures against the original baseline where practical. Correct regressions caused by the skill. If a regression cannot be fixed, revert only the skill's changes; never reset or discard work from the user or other processes. If safe rollback is not possible because the same lines changed concurrently, stop and report rather than risk data loss.
- Report what changed, what is still pending approval, which commands were run, failures that predated the change, and any unresolved problems.
