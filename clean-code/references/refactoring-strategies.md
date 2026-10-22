# Refactoring Strategies and Examples

Apply only after explicit approval of a concrete plan. Prefer small, independently verifiable edits. Optimize readability in the project's idiom, not to maximize numbers of extracted functions or abstractions.

## Transformation patterns

1. **Rename for intent** after checking all references and external name consumers.
2. **Extract cohesive operations** when a step expresses a meaningful concept; preserve evaluation, exceptions and effect order.
3. **Simplify branching** through guard clauses, predicates or explicit branches only when truthiness, short-circuit behavior and ordering remain identical.
4. **Remove semantic duplication** when code represents a shared rule with the same lifecycle; do not conflate coincidentally similar algorithms.
5. **Clarify state and errors** without changing mutations, thrown values, retry timing, resource cleanup or propagation.
6. **Reorganize locality** with specific approval if moving between files, modules or architectural layers.
7. **Preserve comments expressing why** while removing only genuinely redundant comments; synchronize any approved affected docs.

## Values and conditional intent (G25 and G28)

Apply the criteria and source attribution in `clean-code-principles.md`. These examples illustrate choices, not mandatory transformations for all literals or conditions.

### Name a domain limit (G25)

Before:
```typescript
if (attempts >= 3) {
  blockAccess();
}
```

After:
```typescript
const MAX_LOGIN_ATTEMPTS = 3;

if (attempts >= MAX_LOGIN_ATTEMPTS) {
  blockAccess();
}
```

Use this name only if the inspected context establishes login attempts. Preserve the value and comparison boundary; do not invent a rule. Conversely, `if (items.length === 0)` already expresses emptiness and need not introduce a constant for zero.

### Encapsulate a domain decision (G28)

Before:
```typescript
if (user.active && user.emailVerified && !user.suspended) {
  allowAccess();
}
```

After:
```typescript
function canAccessAccount(user: User): boolean {
  return user.active && user.emailVerified && !user.suspended;
}

if (canAccessAccount(user)) {
  allowAccess();
}
```

Assumes boolean fields and a domain rule matching the name. Keep field reads in the same order and evaluate the predicate at the original decision point. An existing `if (isLoading)` is already expressive and needs no wrapper. A local `const canAccessAccount = ...` immediately before the decision can also help a one-off case; that is a contextual adaptation rather than G28's specific function-extraction guidance.

### Keep guarded evaluation lazy

Before:
```typescript
if (user !== null && hasPermission(user)) {
  allowAccess();
}
```

After:
```typescript
function canAccessAccount(user: User | null): boolean {
  return user !== null && hasPermission(user);
}

if (canAccessAccount(user)) {
  allowAccess();
}
```

Do not compute `hasPermission(user)` before the null guard: that would eagerly execute a previously skipped call and could change exceptions or effects. Likewise, preserve outer guards when extracting nested conditions. Do not hoist or cache a boolean across mutations, `await`, loop iterations or other operations that can change the observed state. Keep the predicate at its original call site and preserve call counts, ordering and short circuiting; skip extraction if equivalence is uncertain.

## Multilingual examples (illustrative, not unconditional recipes)

### TypeScript: concise guard with identical null handling

Before:
```typescript
function isEligible(user: User | null): boolean {
  if (user !== null) {
    if (user.age >= 18 && user.active) {
      return true;
    }
  }
  return false;
}
```
After:
```typescript
function isEligible(user: User | null): boolean {
  return user !== null && user.age >= 18 && user.active;
}
```
Assumes `active` is typed boolean and there are no getters with unexpected observable differences. Verify project strictness and callers.

### Python: name an existing concept without altering semantics

Before:
```python
def total(items):
    return sum(x.price * x.quantity for x in items)
```
After:
```python
def total(items):
    return sum(item.price * item.quantity for item in items)
```
Only a local rename; output and iteration are unchanged.

### Go: early return preserving error identity

Before:
```go
func load(id string) (Item, error) {
    item, err := fetch(id)
    if err == nil {
        return item, nil
    } else {
        return Item{}, err
    }
}
```
After:
```go
func load(id string) (Item, error) {
    item, err := fetch(id)
    if err != nil {
        return Item{}, err
    }
    return item, nil
}
```
Check whether `Item{}` and `fetch` semantics fit the actual project; do not replace errors or reorder calls.

### Java: intent-revealing boolean

Before:
```java
boolean canAccess(User user) {
    if (user.isActive() && user.hasPermission()) {
        return true;
    }
    return false;
}
```
After:
```java
boolean canAccess(User user) {
    return user.isActive() && user.hasPermission();
}
```
Preserves short-circuit order; inspect if methods are stateful, exceptions differ or code uses instrumentation.

### SQL: clarity with identical results

Before:
```sql
SELECT customer_id, SUM(amount) AS total
FROM payments
WHERE status = 'paid'
GROUP BY customer_id;
```
Potential refinement: prefer meaningful aliases or formatting *only if consistent with the repository*. Do not move filters between WHERE/HAVING, adjust join kinds, add DISTINCT or change null behavior under the label of Clean Code.

## Before applying each edit

Check call sites and semantic context, establish a behavior-equivalence argument, identify tests, assess dynamic contracts, confirm approved edit scope, then make and verify the smallest change. If the argument is insufficient, skip and report.
