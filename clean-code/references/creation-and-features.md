# Creation and Feature Development

## Scope and intent

Apply Clean Code principles to **new code**, not only to refactoring. Use three modes: Refactor (existing behavior unchanged), Create (new file/component/module), and Feature (new behavior integrated into existing code). An implementation may contain multiple clearly separated modes. Feature additions are allowed only when requirements and changed behaviors are expressly approved in the plan.

## Before writing: inspect and specify

1. Identify the affected language(s), runtime, framework, version, repository instructions, architecture, naming, patterns, package manager, and available test tools. Reuse established patterns; do not impose object orientation or SOLID patterns on unrelated idioms.
2. Capture purpose, location, public API, inputs/outputs, validation, errors, state transitions, accessibility where relevant, data/security constraints, async/concurrency effects, integration points, and acceptance criteria. Mark unknowns; ask only essential questions.
3. Review comparable code and downstream/upstream contracts; list files to create/change, file responsibilities, tests, and validation. Avoid speculative general-purpose layers and premature reuse.
4. For Feature mode, define the **authorized behavior delta**: what becomes possible or changes, what must remain invariant, any migrations or public-contract changes and their risks.
5. Present read-only requirements summary, file-level plan, test plan, assumptions, and risks. **Stop until the user explicitly approves**. No scaffolding, placeholder file, test creation or formatter write before approval.

## During implementation

- Implement the smallest coherent solution that meets approved acceptance criteria. Name concepts by intent and domain language; keep functions focused and boundaries explicit.
- Keep validation, error reporting, logging and cleanup explicit where necessary; respect existing error and observability patterns. Avoid swallowing errors or leaking secrets.
- Respect architectural boundaries, dependency direction and existing abstractions where they are useful; do not introduce new architecture solely to claim compliance with Clean Code.
- Prefer local clarity over needless wrappers, abstractions, factories, getters, or de-duplication of merely similar requirements.
- For UI, follow existing styling, accessibility and state patterns; for backend, follow auth, authorization, serialization and persistence contracts; for scripts/SQL, honor language-specific semantics.
- Create focused tests against **requested behavior** and relevant edge cases, and characterization tests for existing affected behavior when viable. Do not modify unrelated existing tests to disguise regressions.
- Modify only approved files. If a necessary change extends scope or changes the API beyond the approved plan, pause and request renewed authorization.

## Verification and reporting

- Capture existing failure baseline before implementation. Execute relevant available safe checks after implementation and compare against it.
- Validate new behavior using acceptance criteria; verify unaffected behavior and contracts via regression checks. A feature is not expected to be semantically identical to the pre-feature baseline in its intended behavior.
- Report files created/changed, requirement-to-implementation mapping, intentional contract and behavior changes, checks run/skipped, existing versus introduced failures, and remaining limitations.

## Multilingual illustrations (schematic)

### TypeScript: new pure calculation

```ts
function calculateNetAmount(grossAmount: number, discountAmount: number): number {
  return grossAmount - discountAmount;
}
```

Validate domain constraints if the requirements demand them; do not invent validation or currency rounding rules.

### Python: cohesive new parsing function

```python
def parse_item_count(raw_count: str) -> int:
    return int(raw_count)
```

Define error policy from requirements and project patterns; avoid silently catching invalid inputs when callers expect `ValueError`.

### Go: explicit new error handling

```go
func LoadName(input string) (string, error) {
    if input == "" {
        return "", errors.New("name is required")
    }
    return input, nil
}
```

The validation/error contract here is illustrative, not a universal default.

### SQL: explicit columns for new query

```sql
SELECT id, display_name
FROM users
WHERE active = TRUE;
```

Check schema and dialect instead of assuming `active`, identifiers, or Boolean syntax exist.
