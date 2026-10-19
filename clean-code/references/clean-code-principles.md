# Clean Code Principles and Judgment

Use Robert C. Martin's *Clean Code* as the main conceptual reference, not as a collection of universal mechanical constraints. Interpret guidance in context of language, type system, performance, existing conventions and maintainers' expectations. This reference is original guidance, not a reproduction of the book.

## Practical principles

- **Meaningful names:** name intent, domain concepts and units; avoid vague names and misleading abbreviations. Avoid needless verbosity. Consider public compatibility before renaming.
- **Cohesive functions:** make responsibilities discoverable; extract a function when a named concept or separate responsibility becomes clearer. Smallness is a heuristic, not a line-count rule.
- **Single level of abstraction:** avoid mixing orchestration with low-level mechanics when separation actually improves understanding.
- **Clarity of control flow:** reduce needless nesting, use guard clauses when semantics remain unchanged, and express predicates directly when safe.
- **Actual duplication:** reuse the same domain knowledge or behavior where appropriate. Similar-looking code may encode independent business rules and should stay separate.
- **Comments and documentation:** favor expressive code for *what*, retain comments documenting *why*, constraints, tradeoffs, invariants, and external compatibility. Preserve licenses, security warnings and required annotations.
- **Errors:** retain failure conditions, error surfaces, cleanup guarantees, propagation semantics and useful diagnostics. Changing an error is potentially changing behavior.
- **Side effects and state:** make state transitions apparent; do not inadvertently reorder IO, mutations, logging or network interactions.
- **Tests and maintainability:** use existing test styles, and improve readability without turning test fixtures into brittle abstractions.
- **Boy Scout principle:** improve only within the approved scope; do not treat adjacent issues as automatically authorized changes.

## Secondary heuristics

DRY, KISS, SOLID, cohesion, coupling and complexity measures may support a decision, but do not override the main goal, existing contracts, language idioms or simplicity. Favor direct code over speculative patterns, factories, service classes or generalized frameworks.

## Anti-patterns in using Clean Code

- Splitting every expression into a helper that obscures the flow.
- Forcing objects/interfaces on functional or procedural code.
- Enforcing universal maximum lines, branches or parameters.
- Converting explicit readable control flow into opaque clever expressions.
- Eliminating independently evolving domain logic merely because it looks alike.
- Reformatting unrelated files or adjusting tool settings to make a change pass.
- Fixing suspected bugs without separate approval.

## Decision rubric

For each proposal ask: (1) Is there clear evidence of a readability or maintainability problem? (2) Will this change reduce cognitive burden? (3) Is the behavior equivalence justified? (4) Does it preserve repository idioms and external contracts? (5) Is it proportionate to the risk and complexity it adds? If any essential answer is uncertain, investigate or skip and report.
