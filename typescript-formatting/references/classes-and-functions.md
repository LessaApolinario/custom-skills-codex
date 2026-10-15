# Classes, functions, and control flow

## Class members

Group related properties together. Separate the property block, constructor, and each method using one blank line where project style allows. Preserve physical declaration order, initialization order, decorators, access modifiers, and method signatures. Constructor parameters remain on one line if readable and within project conventions; expand when necessary.

```ts
export class UserService {
  private readonly cache = new Map<string, User>();
  private readonly maxRetries = 3;

  constructor(
    private readonly repository: UserRepository
  ) {}

  async find(id: string): Promise<User | null> {
    const user = await this.repository.findById(id);

    if (!user) {
      return null;
    }

    return user;
  }

  clearCache(): void {
    this.cache.clear();
  }
}
```

## Functions and parameters

Leave short signatures on one line, expand long or complex signatures. Do not change default values, overload order, spread arguments, destructured defaults, or return types. Blank lines distinguish meaningful steps in functions.

## Arrow functions

Keep arrow return style and body structure unchanged. Expand existing block-bodied callbacks with multiple operations for readable logical separation; do not turn concise bodies into block bodies or vice versa.

## If, switch, loops, try/catch

Indent and space control-flow bodies correctly. Separate independent steps with blank lines. Preserve switch fallthrough, labels, comments, `finally`, and every scope boundary. Do not insert braces into originally brace-free statements unless an existing project formatter does so with demonstrably safe transformation—and default to no such transformation.

## Nested ternary formatting

Break long/nested conditional expressions along existing parse structure; retain the original operator pairing and precedence. Never replace with an `if`, switch or logical-operator expression.
