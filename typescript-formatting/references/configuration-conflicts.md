# Project configuration and conflict policy

## Precedence

Safety > effective project rules > skill defaults. Read project config without modifying it, and honor ignore rules and generated-file markers. Do not install a formatter, ESLint plugin, or TypeScript dependency. Existing project formatter and lint behavior outrank stylistic examples in this skill.

## Discovery

Check applicable local config (nearest relevant config for target file): package manifest/scripts and lockfile, `.editorconfig`, `.prettierrc*`, `prettier.config.*`, `.prettierignore`, `eslint.config.*`, `.eslintrc*`, lint ignore rules, `tsconfig*.json` and generated markers. Avoid assuming a single repo-root config controls nested packages. Use already installed executable commands; do not invoke `npx`/`yarn dlx` in a way that downloads packages.

## Conflicts

Identify actual conflict with effective formatting settings, not merely a different visual output. If the project would automatically rewrite or lint-against a desired style, prefer the project style for that region and record a concise explanation. Do not produce formatting that oscillates when formatter is run repeatedly. For example, if a project's configuration enforces trailing commas, honor it and report that the skill's no-trailing-comma preference was superseded.

## Imports

The no-reordering safety rule is strict even if lint asks for sorted imports. Treat sort-import diagnostics as existing/out-of-scope issues, not authorization to reorder. Never run bulk auto-fixers capable of applying unrelated rules.

## No formatter present

Conservatively format syntax understood with available tooling, avoiding edits to ambiguous regions. Note which checks were unavailable. Unsupported syntax remains unchanged with a reason in the final report.
