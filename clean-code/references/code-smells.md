# Code Smell Diagnosis

Inspect context and usages before assigning a smell. Record location, evidence, consequence, confidence and the minimal useful intervention. A smell is a refactoring candidate, not necessarily a defect.

## Broad checklist

| Area | Look for | Investigate before recommending |
|---|---|---|
| Names | Uninformative, deceptive, inconsistent names; ambiguous units | Domain terminology, externally referenced symbols |
| Functions | Mixed responsibilities; implicit state changes; convoluted flow | Existing abstractions, call sites, performance-sensitive paths |
| Duplication | Same rule or algorithm maintained in multiple places | Whether similar sections intentionally evolve differently |
| Values | Literals hiding domain rules, limits, units or codes, even in a single occurrence (G25) | Whether meaning is already evident, existing constants, narrowest useful scope, independent rules with equal values |
| Conditionals | Deep nesting, opaque predicates, decisions requiring reconstruction of a domain rule, repeated branching (G28) | Whether a predicate name explains intent better; keep already expressive conditions direct; preserve evaluation order, short circuiting, effects, null behavior and mutable-state reads |
| Modules | Excessive coupling, poor locality, cyclic dependencies | Runtime module resolution and public surface |
| Comments | Stale explanations, comments merely repeating code | Domain rationale, legal notices and required tool directives |
| Errors | Swallowed errors, unclear propagation, inconsistent cleanup | Observable error contracts, logging, retry expectations |
| State | Hidden mutation, surprising aliasing, temporal coupling | Framework lifecycle, ownership, thread/async semantics |
| Parameters/data | Data clumps, positional confusion, boolean blindness | Public signatures, serialization, performance implications |
| Tests | Unclear intent, fragile setup, inadequate characterization | Existing failure baseline and coverage scope |

## Prioritization

Order findings by expected maintainability benefit, impact on comprehension, breadth of affected code, confidence in diagnosis and implementation risk. Distinguish blocking safety concerns, high-value refactors and optional style cleanup. Prefer a small verified improvement to an ungrounded extensive rewrite.

## Reporting conventions

For each finding provide: path/symbol, symptom, principle, concrete evidence, impact, confidence (`high` / `medium` / `low`), safe proposal, possible behavior hazards, and validation approach. Mark probable bugs separately as **not in refactoring scope**. Never claim a hard complexity threshold is an authoritative Clean Code requirement.
