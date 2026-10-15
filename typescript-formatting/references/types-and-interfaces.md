# Types, interfaces, enums, declarations

## Type aliases

Separate exported type aliases with a blank line. Expand object-shaped types with one field per line. For long unions, write one arm per line; keep very short unions inline if appropriate and within project convention. Preserve exact parentheses and precedence; do not rewrite to a different type construction.

```ts
export type UserId = string;

export type UserRole = 'admin' | 'user';

export type User = {
  id: UserId;
  role: UserRole;
};

export type RequestStatus =
  | 'pending'
  | 'processing'
  | 'approved'
  | 'rejected';
```

## Interfaces

One member per line, no unnecessary blank lines between simple fields or method signatures. Blank lines between independent interface declarations. Preserve `extends`, member order, optional/readonly modifiers, overloads, call/index signatures, and documentation. Do not convert between `interface` and `type`.

```ts
export interface User {
  id: string;
  name: string;
  email: string;
}

export interface UserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}
```

## Generics

Expand complicated generic parameter lists and deeply nested types when useful; keep short parameters compact. Preserve bounds and defaults. Do not modify type algebra, distributive conditional types, inference semantics, or type-parameter scope.

```ts
export type RepositoryResult<
  T,
  E extends Error = Error
> = Promise<{
  data: T | null;
  error: E | null;
  success: boolean;
}>;
```

## Enums

One enum member per line, no unnecessary group blanks. Preserve declaration and member order, initializer text, computed values, auto-increment behavior. Do not replace enums with objects. The last enum member has no trailing comma.

## Namespaces, ambient declarations, .d.ts

Visually separate types, interfaces, functions, and unrelated contracts with a single blank line; preserve lexical order and all `declare`, `global`, namespace/module declarations, and ambient augmentation semantics. Do not introduce implementation into a declaration file. No implicit module restructuring or export changes.

## Overloads

Keep overload signatures adjacent with no blank lines between them; put one blank line before the implementation only when compatible with parser/tooling and project formatting. Never reorder overloads, insert other statements into the set, or change overload signatures.
