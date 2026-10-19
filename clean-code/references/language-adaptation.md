# Language and Paradigm Adaptation

Universal support means reasoning from the actual code and available tools, **not** claiming identical refactoring capabilities across ecosystems.

## Discovery

- Identify language, version, dialect, framework, build system, package manager and repository conventions.
- Locate `AGENTS.md` and applicable project instructions, formatter/linter settings, tests, typecheck/build commands and generated-code boundaries.
- Determine whether the code is object-oriented, functional, procedural, declarative, event-driven or mixed. Prefer idiomatic patterns consistent with that paradigm.
- Identify dynamic contracts: reflection, property-name lookups, decorators/annotations, dependency-injection tokens, plugin registration, routes, serializers, database schemas, CLI flags, configuration keys and consumers outside the repository.

## Semantic hazards

| Domain | Guard against |
|---|---|
| JavaScript/TypeScript | `null` vs `undefined`, truthiness, getter effects, evaluation order, promises, event-loop timing, module exports |
| Python | Late binding, descriptors/properties, exceptions, iterators/generators, mutation, import-time effects |
| Java/Kotlin/C# | Public binaries and reflection, overloads, nullable contracts, checked errors, concurrency and resource lifetimes |
| Go/Rust/C/C++ | Ownership and lifetimes, pointer aliasing, defer/destructors, zero values, concurrency, ABI/FFI and undefined behavior |
| SQL | NULL three-valued logic, collation, join cardinality, grouping, transaction isolation and query plans |
| Shell | Word splitting, quoting, globbing, exit status, pipeline/`set -e` semantics and environment changes |
| HTML/CSS/templates | Selector dependencies, accessibility semantics, cascade, hydration and generated markup |

These are examples, not an exhaustive list. Investigate the actual runtime and ecosystem.

## Capability-based execution

Use existing language servers, symbol-safe rename tools, static analyzers, tests, linters and compilers when present. Do not automatically install tools, update dependencies or modify lint/format/build configuration. Missing tool support reduces the set of defensible transformations; explain that limitation instead of claiming safety.

Never impose OOP abstractions on non-OOP languages, or substitute a compact expression for a readable explicit flow merely to shorten the code. Preserve project conventions unless higher-priority instructions require otherwise.
