# Safety, Baseline, Tests and Rollback

## Non-negotiable invariants

- No edits (including newly created files or tests) before explicit approval of a diagnosis/requirements summary and file-level plan, even if skill activation was automatic or the original request asked to create/implement/refactor.
- Refactor: preserve all observable behavior. Create/Feature: permit only explicitly approved new behavior and contract changes; preserve all unaffected outputs, errors, side effects, ordering, state, concurrency, and ownership/cleanup guarantees.
- Protect exported APIs, function signatures, routes, payloads, schemas, storage identifiers, wire formats and other external contracts. Require specific approval for contract changes; distinguish those changes from pure refactoring.
- Respect authorized edit scope. Investigating callers does not authorize changing them.
- Preserve user edits in dirty files. Never reset the working tree wholesale.
- Never hide failing tests or silently fix suspected preexisting defects.

## Baseline checklist

1. Record working-tree status and relevant diffs without overwriting anything.
2. Inventory applicable tests, lint, formatter checks, type checks, builds and specialized checks.
3. Run safe feasible baseline checks, beginning with targeted ones and broadening when relevant. Do not inadvertently change snapshots, generate committed files or trigger external destructive operations.
4. Record actual commands, outcomes, and diagnostics to distinguish preexisting failures from new regressions.
5. If checks cannot run (missing runtime, credentials, network services, extreme cost), record the precise limitation; never mark them passed.

## Characterization tests

When existing coverage is insufficient, propose characterization tests in the plan and create them **only after approval**. Derive assertions from observed current behavior, including edge cases, failures and side effects, rather than hypothetical desired behavior. Preserve existing test expectations, unless an approved safe refactor requires a legitimate test organization change without weakening coverage. Potential bugs must be reported separately and not corrected implicitly.

## Post-change validation

Execute all applicable available, safe and feasible checks, using targeted tests before broader checks in large projects. Compare baseline against final outcomes, and inspect changed behavior semantically beyond test results. Categorize checks as `passed`, `failed (preexisting)`, `failed (introduced)`, `not run`, or `unavailable`. Do not claim tests prove full equivalence.

## Selective rollback

If a regression is introduced: identify the responsible approved edit, undo only that edit (prefer targeted patch/hunk or explicit edit tracking), preserve all original and concurrent user changes, and rerun relevant validations. Never use `git reset --hard`, `git checkout -- .`, or wholesale file restoration that can discard user edits. If edits overlap and provenance cannot be isolated safely, stop automatic rollback and report the conflict, asking for direction.

If behavior-preservation confidence remains inadequate despite inspection and tools, do not apply (or undo) the risky change, and document residual risks and skipped proposals.

## Mode-aware validation

- For Create/Feature, record acceptance criteria and expected new behavior before editing. Tests must assert the approved requirements, not imagined outcomes.
- Compare existing behavior against baseline only where it must remain unchanged; verify the intentional behavior delta separately.
- Any unexpected behavior/contract change is a regression. Preserve existing user edits during isolated rollback; stop if overlap cannot be resolved safely.
