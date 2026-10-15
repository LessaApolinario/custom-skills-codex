---
name: typescript-formatting
description: Format explicitly requested TypeScript, TSX, and declaration files for readable whitespace, spacing, line breaks, and structural layout while preserving behavior, type contracts, comments, directives, project configuration, and existing user edits. Activate for explicit skill calls and natural-language requests to format or improve the readability of TypeScript code; do not activate for general refactoring or bug fixes.
---

# TypeScript Formatting

## Mission

Perform **formatting only**. Do not refactor, rename, reorganize declarations or imports, change types, change return forms, rewrite expressions, fix business logic, or alter project configuration. Preserve runtime behavior and type-level contracts. Work only within explicitly identified files, directories, or globs; never expand scope to related files. Respond in the user's language; skill specifications and reference material are in English.

## Precedence

1. Safety and semantics; protect comments, directives, JSX whitespace, template literal content, side effects, and preexisting edits.
2. Project's effective Prettier, ESLint, EditorConfig, ignore patterns, and other formatting conventions.
3. Preferences documented in this skill, where compatible with 1 and 2.

Report, rather than override, conflicts. Never install dependencies and never modify tooling configuration. Do not run indiscriminate `eslint --fix`; only use fixes known to be formatting-only and safe. If no formatter is installed, perform conservative formatting with available means, noting reduced assurance.

## Execution workflow

1. **Resolve scope.** Determine exact requested .ts, .tsx, and .d.ts files from explicit filenames, lists, directories, or globs. If user requests the current file, use only the file resolved by the working context; do not guess. Exclude generated files, ignored paths, node_modules, build output, and files bearing do-not-edit markers. Do not modify anything outside scope.
2. **Inspect.** Read nearby package scripts and existing config without changing them. Check `.editorconfig`, Prettier configs/ignore, ESLint configs/ignore, repository conventions, TypeScript version, and existing errors when practical. Avoid any unapproved installs.
3. **Capture baseline.** Record the complete original bytes for each target, existing user changes, applicable diagnostics, and, where feasible, parsed syntax structure. Never use `git reset --hard`, `git checkout --`, `git clean`, or destructive restoration.
4. **Plan edits.** Consult [general-formatting.md](references/general-formatting.md), [types-and-interfaces.md](references/types-and-interfaces.md), [classes-and-functions.md](references/classes-and-functions.md), and [framework-specific.md](references/framework-specific.md) as relevant. Consult [configuration-conflicts.md](references/configuration-conflicts.md) for precedence.
5. **Apply.** Prefer an available local formatter consistent with project config, then narrowly scoped formatting-only edits. Use parser-aware approaches where available, never broad regex replacements across source code. Keep operation transactional per file, even for large files.
6. **Validate.** Follow [safety-and-validation.md](references/safety-and-validation.md). Review full diffs, syntactic preservation, tsc/lint diagnostics versus baseline when available, whitespace-sensitive constructs, and second-pass idempotence. Do not claim semantic equivalence is proven by tsc, lint, or superficial AST checks alone.
7. **Rollback failures.** If unsafe or regressive, restore only the edits this skill introduced to that file using its captured pre-edit bytes; preserve preexisting user work. If a region is unsupported or safety cannot be established, leave it unchanged and report why.
8. **Report.** Provide requested scope, files changed/skipped, kinds of formatting performed, validations passed/failed/not run, baseline versus introduced diagnostics, configuration conflicts, rollbacks, and limitations. Avoid claiming completion for skipped checks.

## Baseline style, when compatible with project

- 2 spaces; single quotes when safely convertible; semicolons; no trailing commas; preferred line width 100 (soft limit).
- Blank line between independent top-level declarations and distinct logical blocks; do not insert empty lines indiscriminately.
- One object property per line; expand object-shaped aliases and longer unions; simple interface layout without unnecessary internal blank lines.
- Preserve ordering of imports, exports, overloads, decorators, object properties, and enum members.
- Keep short Zod chains, short parameters, and simple expressions compact when readable; expand complex structures without rewriting expressions.
- Do not reformat actual contents of template literals. Protect TSX whitespace behavior.

## Scope and prohibitions

- Do not generate replacement files or edit generated outputs.
- Do not change class/member visibility, decorators, generics, typing, optionality, evaluation order, literals, exported APIs, or comments' meaning.
- Do not reorganize imports, even if lint suggests it; report those issues.
- Do not adjust types or fix preexisting errors. Files with preexisting issues may still be formatted if there are no introduced regressions.
- Ignore unsupported constructs safely and report them.
- Be idempotent: the second formatting pass must yield identical content or report failure and revert unsafe formatting.
