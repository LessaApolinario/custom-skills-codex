---
name: clean-code
description: Apply Robert C. Martin-inspired Clean Code principles when analyzing/refactoring existing code, creating new files/components/modules, or implementing new features across languages and paradigms. Use on explicit invocation or clear Clean Code intent. Inspect repository context, diagnose existing code or clarify requirements for new code, propose a concrete file-level implementation plan, and always require explicit approval before any edits. Preserve existing behavior outside intentional feature changes. 
---

# Clean Code — Creation, Features, and Refactoring

## Mission and priorities

Create and improve readable, maintainable code based primarily on the *Clean Code* principles popularized by Robert C. Martin, adapted to the target language, paradigm, repository conventions, and constraints. Operate in three distinct modes: **Refactor** (behavior-preserving changes), **Create** (new files/components/modules), and **Feature** (implement requested functionality). New behavior is allowed only as explicitly specified and approved in Create/Feature mode; unrelated bug fixes, unrequested architectural redesigns, and arbitrary style metrics are not authorized.

Priority order:
1. Preserve existing behavior outside the explicitly approved new functionality, protect public contracts, and preserve user edits.
2. Obey higher-priority instructions, the approved scope, and applicable repository instructions (e.g. `AGENTS.md`).
3. Honor established project conventions and configured tooling.
4. Improve expressiveness, cohesion, simplicity, and maintainability without needless abstractions.
5. Communicate uncertainties, evidence, tradeoffs, and verification limitations honestly.

## Activation and authorization

- Support explicit invocation and automatic recognition of **clear Clean Code intent** for refactoring, creation, or feature development. A generic request to implement a feature does not by itself authorize unrelated refactoring or activation without a Clean Code signal. Select the most appropriate mode (Refactor, Create, Feature); mixed requests must identify each mode and its authorized scope.
- Activation authorizes **read-only analysis**, not edits. **Always** present a diagnosis and concrete proposed change plan and **wait for explicit user approval before modifying any file**, including new files, tests, formatting, and documentation. An initial request to "create this" / "implement this feature" / "refactor this" does not waive this gate.
- If the plan materially changes (expanded scope, new structural changes, or new risks), obtain renewed approval. Significant moves across files/modules or architectural adjustments require specific approval even if a general plan was approved.
- Ask questions only when needed to make a safe or materially consequential decision. Otherwise omit uncertain changes, document them, and continue only with safe, independent approved work.
- Accept explicit targets as files, directories, globs, the entire project, or clearly specified new files/features. Inspect related code and contracts as needed but **edit only approved targets**; propose scope expansion before editing anything outside them.

## Mandatory workflow

## Mode selection and requirements

- **Refactor:** the current code is the behavior baseline. Do not introduce new features or silently fix suspected bugs.
- **Create:** capture the requested responsibility, file location, interface, expected behavior, edge cases, and integration points. Do not invent requirements or layer abstractions unnecessarily.
- **Feature:** identify acceptance criteria, entry points, affected consumers, new behavior, and existing behavior that must remain unchanged. The approved feature spec defines the **intentional behavior delta**; compare it explicitly against existing contracts and invariants.
- For mixed tasks, partition changes by mode. Do not mislabel feature or contract changes as pure refactoring. See `references/creation-and-features.md`.

### 1. Understand and inspect (read-only)

- Identify target language(s), paradigms, frameworks, build and test commands, format/lint/typecheck configurations, repository guidance, and current working-tree changes.
- Inspect dependencies, callers, consumers, serialized interfaces, side effects, concurrency and asynchronous behavior to the extent relevant. For Create/Feature, inspect analogous implementations and entry points, requirements and test conventions, and infer no undocumented business rules. Do not assume that a successful compile guarantees safety.
- Read `references/clean-code-principles.md`, `references/code-smells.md`, and `references/language-adaptation.md` for context as necessary. Follow relevant repository instructions unless they conflict with higher-priority instructions.
- Keep a precise distinction between **inspection scope** and **authorized edit scope**.

### 2. Establish baseline (read-only)

- Inventory all applicable existing verification tools and commands. Execute safe and feasible baseline checks prior to any edit; record command, scope, exit status, outcomes, and existing failures.
- Prefer targeted checks first when the repository is large, then broader applicable checks when feasible. Avoid destructive, network-dependent, expensive, or environment-mutating commands when inappropriate; report skips and why. "Complete baseline" means a complete **accounting** of applicable checks, not blindly running every possible costly task.
- Note any uncommitted or otherwise preexisting changes without resetting them. Characterization and new-feature tests may be proposed here, but **not created** before approval.

### 3. Diagnose or specify, then propose plan (read-only; approval gate)

- In Refactor mode, identify significant and minor code smells comprehensively, with file/line or symbol, observed evidence, relevant principle, impact, proposed minimal transformation, risks, dependencies, and verification method.
- In Create/Feature mode, translate the request into testable acceptance criteria, identify missing requirements and assumptions, and outline intended behavior changes, files to create/change, integration points, risks, and testing. Ask only for decisions essential to safe implementation; do not silently infer critical requirements.
- Classify refactor observations by impact and confidence; do not treat style preferences as correctness defects. Explain possible existing bugs separately and **do not fix them** under a refactoring-only approval.
- Provide an ordered, bounded plan with exactly which files may change, potential test additions, any structural moves requiring special approval, and validation steps. Specify which existing behavior and exposed contracts remain unchanged, and explicitly enumerate any intentional approved behavior or contract changes for Create/Feature mode.
- **STOP. Obtain explicit user approval before any write.** Do not auto-continue within the same run merely because the skill triggered automatically.
- See `references/reporting.md` for templates.

### 4. Apply only approved safe changes or additions

- Follow `references/refactoring-strategies.md`, `references/creation-and-features.md`, and `references/language-adaptation.md` according to mode.
- Prefer semantic naming, cohesive functions, clear control flow, reduced genuine duplication, and helpful documentation over rigid rules such as maximum line counts or parameter counts.
- In Refactor, Create and Feature modes, review values whose meaning is not evident and conditions that hide their intent. Apply G25 (named constants) and G28 (encapsulated conditionals) contextually; do not mechanically extract every literal or compound condition. See `references/clean-code-principles.md` for criteria and `references/refactoring-strategies.md` for examples and evaluation hazards.
- Rename internal symbols only when their complete use can be established; exercise special caution for exported/public names, data shapes, reflection, dynamic lookup, dependency injection keys, persistence names, generated code, and integration points. Public contracts require **specific authorization** to change; in Feature mode, only contract changes explicitly approved in the plan may be made. Never label behavior-changing work as pure refactoring.
- In Refactor mode, preserve outputs, thrown errors, exception categories where observed, evaluation and side-effect order, state mutations, timing/concurrency assumptions, resource ownership, and externally observable interactions. In Create/Feature mode, preserve these properties of **unaffected** existing behavior and implement only the approved behavioral changes.
- Do not change project lint/formatter/build configuration or install packages/tools automatically. Use available tooling; report missing capabilities. Do not make unrelated changes.
- Write characterization tests for existing behavior and tests for new acceptance criteria when feasible and covered by the approved plan. Characterize existing behavior (including suspected defects) rather than inventing idealized expectations. Never weaken assertions, delete failing tests, or update snapshots solely to hide a regression.
- Protect every preexisting user change. Track your own edits at a fine-grained level; never use sweeping destructive rollback commands such as `git reset --hard` or `git checkout -- .`.

### 5. Verify, compare, and recover

- Run all applicable **available, safe and feasible** tests, lint, type checks, compilation/build, and ecosystem-specific checks as relevant; compare results to baseline. Explain any unrun checks and any remaining preexisting diagnostics.
- If a regression is introduced, selectively undo **only the skill's changes** responsible for it, keeping preexisting user edits intact; revalidate afterward. Never overwrite an ambiguous overlapping user change in an attempt to roll back. If isolation is impossible, stop automatic rollback, identify the conflict, and ask for guidance.
- For Refactor or unaffected existing behavior, if equivalence cannot be reasonably established, omit or undo the uncertain transformation and document why. For new behavior, validate against approved acceptance criteria and report unresolved uncertainties. A clean test run alone is not proof of complete behavioral equivalence.

### 6. Report

- Provide a detailed final report: mode, created/edited files and principles applied, before/after rationale where relevant, acceptance criteria and intentional behavior changes (Create/Feature), preserved contracts and existing behavior, tests added, commands and outcomes compared with baseline, identified-but-unfixed bugs, skipped proposals, remaining risks, and limits of confidence.
- Clearly distinguish `passed`, `failed (preexisting)`, `failed (introduced)`, `not run`, and `unavailable`. Never imply that a check ran when it did not.
- Use `references/safety-and-validation.md` and `references/reporting.md` for criteria and format.

## Reference loading

- `references/clean-code-principles.md`: value judgments, simplicity, naming, functions, comments.
- `references/code-smells.md`: broad diagnosis and prioritization.
- `references/refactoring-strategies.md`: safe transformations and multilingual examples.
- `references/creation-and-features.md`: new files/features, requirement mapping, acceptance criteria and mode-specific examples.
- `references/language-adaptation.md`: language/paradigm adaptation and dynamic boundaries.
- `references/safety-and-validation.md`: baselines, behavior, test strategy, isolation and rollback.
- `references/reporting.md`: diagnosis, approval proposal, and detailed final report templates.

These reference files guide judgment; repository-specific instructions and the user-approved plan govern the actual scope. If safe implementation cannot be established, provide a useful diagnosis or requirements gap rather than editing speculatively.
