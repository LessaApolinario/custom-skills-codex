# Reporting Templates and Approval Protocol

Adapt headings to the task and project. Be concrete, concise where possible, and fully transparent about verification.

## Read-only diagnosis and plan — approval required

```text
Scope requested: <files/directories/globs/project>
Inspection context: <relevant dependencies and consumers, without edit authorization>
Language and conventions: <facts>
Preexisting modifications: <status, with no overwritten work>
Baseline: <checks run and results; not-run/unavailable checks and reasons>

Findings:
- <path:symbol>: <specific evidence and code smell>
  Principle: <clean-code principle>
  Impact / confidence: <...>
  Proposed change: <smallest useful change>
  Behavioral / contract risk: <...>
  Verification: <...>

Suspected bugs (not included in refactoring): <...>

Proposed edit plan:
1. <approved target, intended edit, and test implications>
2. <...>
Structural changes requiring separate approval: <...>
Out-of-scope and uncertain suggestions: <...>

Approval request: May I apply exactly this plan? No files have been changed.
```

Never perform the proposed edits until user approval is received. If new risks or a materially wider plan emerge later, pause and seek renewed approval. Ask only necessary questions; otherwise skip uncertain independent changes.

## Final report — after authorized edits

```text
Approved scope and plan: <...>
Files changed:
- <path>: <what and why; relevant Clean Code principle>
Behavior and public contracts: <preserved, with qualifications>
Tests created/modified: <paths and observed behavior covered>

Validation compared to baseline:
- <command> | baseline: <outcome> | final: <outcome> | status: <category>
- <not run/unavailable check> | reason: <...>

Regressions and recovery: <isolated rollback and verification, or none>
Suspected bugs left unfixed: <...>
Skipped uncertain changes: <...>
Remaining risks and confidence limits: <...>
```

Use the status vocabulary `passed`, `failed (preexisting)`, `failed (introduced)`, `not run`, `unavailable`. Make no unsupported claim that behavior is guaranteed identical. Differentiate inspected files from edited files and tests planned from tests actually run.

## Create / Feature plan addendum

```text
Mode: Create | Feature | Mixed
Requested outcome and acceptance criteria: <requirements and measurable scenarios>
Assumptions or essential open questions: <...>
Existing behavior/contracts to preserve: <...>
Intentional behavior or contract delta: <explicitly approved changes>
Files to create or modify: <exact paths, role of each file>
Implementation strategy: <short bounded sequence>
Tests and verification: <new acceptance tests + regression tests + baseline comparison>
Risks and dependencies: <...>
Approval required before any file is created or modified.
```

## Create / Feature final report addendum

Report each acceptance criterion as implemented/partial/not implemented, created and modified files, intentional changes, regression preservation, test and tool results, and any assumptions or unsupported claims. Never describe a planned but unverified criterion as tested.
