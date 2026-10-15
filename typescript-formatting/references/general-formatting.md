# General formatting rules

These rules apply **only when compatible** with effective project configuration and safety constraints.

## Whitespace and punctuation

Use two-space indentation, spaces around operators and inside named import braces, semicolons, single-quoted strings where conversion is safe, no trailing commas, and preferred soft width of 100 columns. Preserve original line ending scheme when feasible. Never break literal values or semantic strings merely to meet width.

Before:
```ts
import {z} from 'zod';
const envSchema=z.object({name:z.string()});
```
After:
```ts
import { z } from 'zod';

const envSchema = z.object({
  name: z.string()
});
```

## Objects, arrays, and destructuring

One property per line in multi-property object literals, including nested configuration objects and objects passed to decorators. Keep an object literal as a structural expression; never reorder keys, reassign values, or change spread positions. Small array-of-object entries are adjacent (no blank lines between items). Expand multi-property destructuring across lines; preserve aliases, defaults, rest, and evaluation order.

```ts
const settings = {
  endpoint: '/api',
  retries: 3
};

const users = [
  {
    id: '1',
    name: 'Ana'
  },
  {
    id: '2',
    name: 'Carlos'
  }
];

const {
  id,
  name,
  email
} = user;
```

## Imports and exported declarations

Preserve import and export order and import specifiers; correct whitespace only. Add exactly one blank line between independent top-level declarations where project conventions permit. Never reorder side-effect imports. Keep comments and attached documentation with their intended statements.

## Logical blank lines

Separate validation/guard blocks, data retrieval, processing, and return statements where readability benefits; do not isolate every one-line statement automatically.

```ts
const user = await repository.findById(id);

if (!user) {
  throw new Error('User not found');
}

return user;
```

## Conditional expressions and arrows

Break nested ternaries across lines, retaining exact grouping and branch association. Keep simple arrow expressions as originally written; do not introduce or remove braces, explicit returns, or parentheses with possible meaning changes. For already block-bodied callbacks, format internal statements and logical groups.

```ts
const label = isLoading
  ? 'Loading'
  : hasError
    ? 'Error'
    : 'Ready';
```

## Non-goals

No import sorting, declaration sorting, type conversions, object-key sorting, code deduplication, renaming, stylistic refactors, or language upgrades.
