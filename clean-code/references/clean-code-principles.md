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

## Meaningful values and conditions

Robert C. Martin's *Clean Code*, chapter 17, presents **G25: Replace Magic Numbers with Named Constants** (p. 300) and **G28: Encapsulate Conditionals** (p. 301). The [publisher's sample](https://ptgmedia.pearsoncmg.com/images/9780132350884/samplepages/9780132350884.pdf) lists these heuristics. The following guidance paraphrases and adapts them; it is not a quotation from the book.

- **G25:** replace values whose meaning is not self-evident with named constants expressing their domain purpose and, where useful, unit, such as `MAX_LOGIN_ATTEMPTS` or `RETRY_DELAY_MS`. A single occurrence can still hide a rule. Prefer an existing suitable constant; place new constants in the narrowest useful scope. Avoid names that merely spell out the value, such as `THREE`.
- Keep literals when their meaning is already clear in context, such as `items.length === 0` for emptiness. Neither a blanket literal ban nor a blanket exemption for particular numbers is appropriate. The same numeric value can represent independent rules; do not merge constants solely because their values match.
- **G28:** encapsulate a condition in a function or method whose name explains the decision when the expression hides intent or forces the reader to reconstruct a domain rule. Choose names such as `canAccessAccount(user)` rather than vague labels such as `checkCondition(user)`. Complexity and repetition are signals, not mandatory extraction thresholds.
- A local semantic boolean can clarify a one-off decision; this is a contextual adaptation, not G28's specific recommendation to extract a function. Keep already expressive predicates and straightforward conditions direct when another name would add indirection without improving understanding. Compound conditions do not automatically require extraction.
- Preserve short circuiting, evaluation order and timing, exceptions, effects and mutable-state reads. Evaluate local booleans at the original decision point; do not cache a decision across state changes or eagerly evaluate guarded expressions. See `refactoring-strategies.md` for examples.

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
