# Safety and validation protocol

## Absolute restrictions

Only formatting. Never change behavior, type contracts, symbol identities, exported signatures, import order, decorator order, property order, literal content, comments' intent, semantically meaningful whitespace, or generated code. Do not fix unrelated warnings, perform refactors, or modify tooling config. Generated files are always skipped, including when a user explicitly targets the directory containing them.

## Baseline preservation and atomic rollback

Before each edit, capture full bytes of each requested file and any preexisting dirty state. Use reversible, scoped edits. For failures, restore the exact captured pre-edit version of the affected file only if doing so will not overwrite concurrent user edits; otherwise stop and report rather than clobber work. Never use destructive Git reset/checkout/clean. Treat large files as a single transactional validation unit, though parsing/editing may happen in passes.

## Protected areas

Preserve template literal raw text, JSX whitespace meaning, string value semantics (single-quote conversion only if escape-safe), file directives (including line-based suppression attachment), embedded language segments, unusual syntax, generated-file markers, and all comment content. If unable to safely format a region, skip it and record the limitation.

## Verification plan

1. Check the final full-file diff; reject unexpected symbol, structure, literal or behavioral changes.
2. Where parser/TypeScript AST tooling exists, compare before/after syntax with location, comments and allowable layout differences normalized **carefully**. AST equivalence alone is insufficient: verify literal raw text, directives, JSX text, escaping and side-effect order separately. If tooling lacks adequate checks, limit edits and disclose reduced certainty.
3. Run supported syntax/typecheck/lint checks without modifying configuration. Compare exact diagnostic identity (file, code/rule, message, relevant range) against pre-edit baseline, allowing reasonable line mapping; do not rely only on counts. Preexisting diagnostics are allowed when no new regressions are introduced.
4. Apply the same formatting process to a temporary/in-memory copy and compare exact content to confirm idempotence. A second pass that changes output is a failure.
5. If an edit introduces a regression or safety concern, roll back that file's changes (without losing preexisting work) and report the cause. Keep safe independent changes in other requested files.

Typecheck may encompass the whole project even though editing is restricted to selected files. Avoid launching project-wide fix commands. If tools, scripts or build checks are unavailable or fail for unrelated reasons, classify checks as 'not run' or 'inconclusive' rather than passed. Never claim proof of equivalence based solely on tests/lint/tsc.

## Final report template

- Scope: requested paths/globs and resolved number of targets.
- Modified: file list and formatting categories applied.
- Skipped: generated/ignored/unsupported paths and reasons.
- Validation: full diff, syntax/AST safeguards, TypeScript, lint, idempotence; pass/fail/not run.
- Diagnostics: baseline versus introduced problems, with concrete identifiers.
- Conflicts: effective project rules overriding skill preferences.
- Rollbacks/limitations: files restored, unresolved uncertainty, and inability to run checks.
