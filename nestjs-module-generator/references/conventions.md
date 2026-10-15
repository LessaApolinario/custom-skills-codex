# TypeScript Conventions and Nest CLI

## Required style

- Use `kebab-case` filenames, `PascalCase` classes, and `camelCase` functions, properties, and variables, except where externally required conventions differ.
- Strongly type contracts and architectural boundaries; prefer safe inference for obvious locals. Avoid `any`, `as unknown as`, unnecessary non-null assertions, and unsafe casts.
- Always use `{}` blocks for `if`, `else`, `for`, `while`, and callback bodies.
- Do not write single-line conditional returns.
- Value-returning callbacks must use block bodies with explicit `return`, including callbacks in `map`, `filter`, `find`, and related methods.
- Prefer `async/await` when appropriate and follow the project's error-handling approach.
- Avoid obvious comments, duplicated logic, unnecessary dependencies, and empty facade files.

## Examples

**Do not write:**

```ts
if (!user) return null;

const ids = users.map((user) => user.id);

const activeUsers = users.filter((user) => user.active);
```

**Write:**

```ts
if (!user) {
  return null;
}

const ids = users.map((user) => {
  return user.id;
});

const activeUsers = users.filter((user) => {
  return user.active;
});
```

Follow the existing formatter/linter where it does not contradict these explicit style requirements. Do not reformat unrelated files.

## Nest CLI workflow

1. Find the correct workspace/package and inspect `nest-cli.json`, `package.json`, the package manager, and local CLI version.
2. Check that target files do not already exist.
3. Prefer official generators when suitable (`nest generate module`, `controller`, `service`, etc.). Check the exact command's available flags for the installed version.
4. Explain the exact command and expected changes; **ask for approval before every effective generator execution**.
5. When supported, run `--dry-run` to inspect affected files before changing them.
6. Generate only the missing artifacts, avoiding destructive overwrite flags and unnecessary scaffolded CRUD routes.
7. Review generated imports, exports, provider registration, tests, and nesting. Remove only unwanted newly generated files after explaining the change; never silently delete existing work.

## Consistency checks

- Ensure module names, class names, provider tokens, file paths, and test filenames match the repository.
- Verify imports are resolvable and that generated providers have all their dependencies registered.
- Prefer focused edits; seek approval for structural refactors.
- Keep all user-facing plans, clarifications, comments, test descriptions, and completion reports in English, except immutable existing business/API identifiers.
