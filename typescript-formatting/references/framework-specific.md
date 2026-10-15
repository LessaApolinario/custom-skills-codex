# Framework-specific and delicate syntax

## NestJS / decorators

Use one decorator per line; preserve exact order and target. For decorator call arguments containing object literals, expand the object properties one per line. Preserve object key order, metadata expressions, and executable decorators.

```ts
@ApiOperation({
  summary: 'Create user'
})
@ApiResponse({
  status: 201,
  description: 'User created'
})
@Post()
async create(@Body() body: CreateUserDto) {
  return this.service.create(body);
}
```

## Zod

Keep short validation chains compact, e.g. `z.string().min(3).max(100)`. Expand schema object properties one per line. Don't replace `.email()` or other API methods, reorder validation refinements, or alter error messages. No dependency-version migrations.

```ts
const userSchema = z.object({
  name: z.string().min(3).max(100),
  email: z.email(),
  age: z.number().int().positive().optional()
});
```

## React and TSX

For multi-prop elements, prefer one prop per line when safe; single short props may stay inline. **JSX whitespace and text rendering are sensitive to line breaks.** Never mechanically add blank lines between child text/inline nodes or alter text-node spacing. Avoid touching a JSX region when safe equivalence cannot be established. Preserve prop and spread order, hooks and event-handler expressions.

```tsx
<UserInfo
  name={user.name}
  email={user.email}
/>
```

## Template literals and embedded languages

Preserve all characters inside template literals, including indentation, line endings, `${...}` boundaries, and embedded SQL/HTML/CSS. Formatting expression tokens inside `${...}` is permitted only with a parser-aware, proven-safe approach and only if it does not alter literal raw segments; otherwise leave unchanged.

## Comments, JSDoc, and directives

May adjust outer comment wrapping/indentation only without changing its content or attachment. Preserve intentional whitespace in ASCII art, code samples, markdown tables and lint suppressions. Never alter the position/target meaning of `eslint-disable`, `prettier-ignore`, `@ts-expect-error`, `@ts-ignore`, or pragmas.

## React / Next.js destructuring correction

Framework detection is context, not authority over quotes, semicolons, trailing commas, wrapping or indentation. Never assume a frontend project uses a particular style. For component-argument prop destructuring and values destructured from hooks, preserve short single-line forms if they fit within the effective per-file formatter width and the formatter keeps them; do not expand solely on binding count. Example: `const { data, error, loading, refetch } = useUsers();` and `function Card({ title, price, currency }: Props) { ... }`. JSX attributes can be wrapped according to effective formatter output; do not force a prop-per-line approach when save reverses it.
