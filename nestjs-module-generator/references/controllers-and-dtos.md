# Controllers, DTOs, and HTTP Contracts

## Rules

- Use a separate file for each relevant contract, e.g. `create-user.dto.ts`, `update-user.dto.ts`, `list-users-query.dto.ts`, and `get-user-params.dto.ts`.
- Use Zod and export both the schema and the inferred TypeScript type; **DTO classes are not required**.
- Check the installed Zod version before selecting its API (e.g. `z.string().email()` vs. `z.email()`).
- Validate all incoming external `body`, `params`, and `query` fields on routes that consume them. Carefully handle coercion, defaults, bounds, filters, and identifiers.
- Connect each schema to a real runtime pipe or adapter. A `z.infer` type does not perform validation by itself.
- Reuse the established integration. If none exists, recommend `nestjs-zod`; note that `createZodDto` classes represent a different style from the chosen schema-plus-type format. If the adapter requires DTO classes, ask before changing conventions or propose a compatible custom pipe.
- Do not install `nestjs-zod` or modify global pipes without approval.

## DTO example

```ts
import { z } from 'zod';

export const createUserSchema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
});

export type CreateUserDto = z.infer<typeof createUserSchema>;
```

## Runtime validation example

The following is illustrative; prefer the actual project's pipe or adapter:

```ts
import { ArgumentMetadata, BadRequestException, Injectable, PipeTransform } from '@nestjs/common';
import { z, ZodType } from 'zod';

@Injectable()
export class ZodValidationPipe implements PipeTransform {
  constructor(private readonly schema: ZodType) {}

  transform(value: unknown, _metadata: ArgumentMetadata): unknown {
    const result = this.schema.safeParse(value);

    if (!result.success) {
      throw new BadRequestException(z.treeifyError(result.error));
    }

    return result.data;
  }
}
```

Check Zod compatibility before using this example. Prefer the existing error-response format over introducing an incompatible one.

## Controller boundaries

- Controllers receive and validate request data, invoke services/use cases, and return approved response shapes.
- No direct ORM calls or business orchestration in controllers.
- Apply the project's authentication guards, role/policy decorators, filters, and interceptors as appropriate.
- Never assume a route should be public or private without inspecting existing rules; ask if ambiguous.
- Do not automatically add API versioning.

## Swagger

If Swagger is configured, document each new endpoint, request schema, response, status, and authentication requirement with the project's established approach. Ensure documented contracts match runtime validation; schema-plus-type definitions alone may not automatically generate Swagger models.

## HTTP responses and errors

Follow existing REST conventions and response envelopes. Use appropriate HTTP status codes and avoid leaking persistence exceptions, stack traces, secrets, or internal data in public responses.
