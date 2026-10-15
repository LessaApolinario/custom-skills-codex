# Mandatory save-time formatting discovery and compatibility

## Critical rule

The user's observed Ctrl+S behavior is the acceptance target. Before touching TS/TSX, discover **which tool actually formats or fixes the file on save**. ESLint configuration alone is insufficient; VS Code may call Prettier, ESLint source.fixAll, TypeScript's built-in formatter, or several passes. Framework name is not a style guide. Never choose semicolons, quotes, trailing commas, line width, object wrapping, destructuring or JSX props based on React/Next vs Nest/Node stereotypes.

## Investigation order (per target path / workspace)

1. Identify monorepo/package boundaries and file kind (`.ts`, `.tsx`, `.d.ts`). Inspect accessible `.vscode/settings.json`, workspace `*.code-workspace` configuration, and relevant language overrides `[typescript]`, `[typescriptreact]` for `editor.defaultFormatter`, `editor.formatOnSave`, `editor.codeActionsOnSave`, `eslint.format.enable`, and relevant Prettier settings. Check nested package settings. Editor **user-level** settings and active installed extensions may be inaccessible: explicitly label these unknown, rather than pretending repository files are exhaustive.
2. Inspect `package.json` scripts/dependencies and local `eslint.config.*` or `.eslintrc*` including extends/overrides, `prettier.config.*` / `.prettierrc*` / package prettier key / `.prettierignore`, `.editorconfig`, eslint ignore, and package/workspace structure. Look for `eslint-config-prettier`, `eslint-plugin-prettier`, `@stylistic` and any formatter plugin. Respect config inheritance and nested overrides.
3. Determine **effective configuration for the exact file**, not just the file that declares a rule. When a locally installed binary is available, read-only discovery commands may include `./node_modules/.bin/eslint --print-config path/to/file.ts` and `./node_modules/.bin/prettier --find-config-path path/to/file.ts`; use the project's package-manager equivalent only if it does **not download/install**. `prettier --file-info` helps identify ignored files. Avoid printing irrelevant secrets.
4. Resolve formatting values actually in force, if known: `singleQuote`, `semi`, `trailingComma`, `tabWidth`/indent, `printWidth`, `bracketSpacing`, `objectWrap`, `singleAttributePerLine`, import formatting, parser/plugins and any stylistic ESLint overrides. Separate configured rule from apparent formatting behavior, especially when two tools run.
5. If possible, run the local formatter **read-only** against sample or file content using stdin, temporary files outside the repo, `--check`, or an equivalent non-writing API. Check a concise sample of destructuring, object literals, JSX attrs, quote, semicolon and comma decisions. Never use `--write` on the original as a discovery step. Simulate save-time ESLint corrections only if safe, non-mutating, and restricted to formatting rules. Do not indiscriminately run `eslint --fix`.
6. Produce an internal per-file style map: source of authority, confidence, quotes, semicolons, trailing commas, indent, width, destructuring wrap, JSX props wrap, object literals, formatter sequence and unknown editor-only settings. Ask a **single concise, bundled clarification** only when a material unknown remains (e.g. which formatter VS Code uses, or whether editor save behavior or existing config should control). If user is unavailable or declines, use only safe conventions supported by config/dry-run, skip uncertain changes and report them.

## Scope-specific wrapping policies

- **Destructuring** is NOT an object-literal declaration. `const { name, email, role, status } = useUser()` should remain one line when it fits the effective width and the formatter keeps it. Same for `function Component({ name, email, role }: Props)`. Do not force each property onto its own line. If the formatter emits multiline, mirror its style.
- **Object literals** (`const x = { ... }`) may follow one-property-per-line as a preference only when preserved by formatter. Do not confuse with object patterns (`const { ... } = obj`), import/export specifiers or JSX properties.
- **JSX props**: follow the actual TSX formatter's `singleAttributePerLine` / equivalent, not unconditional one-per-line. Preserve rendering-sensitive whitespace.
- **Framework context**: React, Next.js, NestJS, Vue, Express etc. must not override effective file-level style.

## Stability test (required)

After every intended edit, run the detected formatter on a copy/stdin where possible. A proposed formatted result is rejected or adjusted when it would be rewritten by that formatter. Where saving also applies ESLint auto-fix, validate against that second stage if a safe, read-only simulation is available. If the editor uses a global formatter unavailable to Codex, say so and ask for formatter name or a before/after save example. Do not promise compatibility without verification. Repeat the complete proposed formatting pass for idempotence.

## Conflict precedence

1. Semantic and safety preservation.
2. Confirmed save-time behavior and effective per-file project/tool formatting.
3. Closely established repository convention when above cannot be determined.
4. Skill preferences as a last resort only.

If an explicit user instruction conflicts with the effective save-time formatter, explain the conflict and seek permission before making a save-unstable edit; never quietly impose it. Never install dependencies, change VS Code settings, or rewrite lint/formatter configuration.

## Reporting

Report the detected formatter(s), relevant applicable config(s), effective formatting rules or unresolved unknowns, whether save-time behavior was simulated, actual style decisions, and specific preferences skipped to avoid save-time reversals.
