---
name: format-sfc-vuejs
description: Safely organize and format Vue.js single-file components. Use when Codex needs to edit, normalize, check, or refactor .vue SFC files, especially script setup ordering, section ordering, Vue macros, Vue Router composables, Pinia stores with storeToRefs, refs, reactives, computeds, functions, hooks, watchers, template/style indentation, or conversion from traditional script to script setup with explicit authorization.
---

# Format SFC Vue.js

## Core Rule

Preserve behavior before visual order. Never remove code, alter business logic, rename bindings, reorder side effects, or convert risky code silently.

Use 2 spaces everywhere, no tabs, and semicolons in JavaScript/TypeScript.

For the complete ordering and formatting standard, read `references/vue-sfc-formatting-rules.md` before changing any `.vue` file.

## Workflow

1. Identify target files. Accept a file path, directory, glob, stdin content, or a check/write style request.
2. Parse every SFC into blocks. Preserve all block attributes, languages, comments, custom blocks, and multiple style blocks.
3. If a file has a traditional `script` block and no `script setup`, stop for that file and ask for authorization before conversion. In non-interactive work, require an explicit instruction equivalent to `--convert-script-setup`; require a second explicit instruction equivalent to `--allow-partial-conversion` for risky partial conversions.
4. Read the detailed reference and classify top-level `script setup` declarations before moving anything.
5. Apply only safe formatting and safe movement. If a declaration has initialization dependencies or side effects, keep the needed relative order and emit a warning.
6. Format `template` and `style` blocks conservatively unless the user asks not to. Do not reorder template nodes, directives, attributes, CSS declarations, selectors, or style values unless explicitly requested.
7. Validate before writing: parse the SFC again, validate script syntax, confirm block closing tags and attributes, confirm declarations were not lost, confirm store/storeToRefs adjacency, confirm 2-space indentation and no tabs, and check idempotence when practical.
8. Write only after validation succeeds. Preserve the original file on failure.

## Section Order

Order SFC blocks as:

1. `script setup`
2. `template`
3. `style`
4. custom blocks, such as `i18n` or `docs`

Keep exactly one blank line between blocks. Preserve multiple `style` blocks and their relative order. Do not create empty blocks.

## Traditional Script Conversion

Do not convert a traditional `script` block silently.

Ask:

```text
The component does not use <script setup>.

Do you want to convert it to <script setup> before formatting?

1. Convert to <script setup> and format
2. Skip this file
3. Cancel the operation
```

For multiple files, also offer:

```text
4. Convert all compatible components to <script setup>
5. Skip all components that do not use <script setup>
```

Warn and ask again before partial conversions when encountering mixins, extends, decorators, plugin/global `this` access, `$refs`, `$attrs`, `$slots`, `$parent`, `$root`, `$route`, `$router`, `$store`, `$t`, `$i18n`, `$nextTick`, Vue 2 filters/APIs, dynamic property names, or plugin-specific options. Mark unresolved pieces with `// TODO(format-sfc-vuejs): ...`.

## Script Setup Order

Use this group order, with exactly one blank line between non-empty groups:

1. imports
2. types, interfaces, enums, namespaces, type-only declarations
3. `defineOptions`
4. `defineSlots`
5. `defineEmits`
6. `defineProps` and `withDefaults(defineProps())`
7. `defineModel`
8. `let` variables
9. ordinary `const` variables
10. `useRoute` and `useRouter`
11. composables
12. Pinia stores and their associated `storeToRefs`
13. other safe top-level calls
14. `inject`
15. refs
16. reactives
17. `toRefs` and `toRef`
18. computeds
19. functions
20. hooks
21. watchers
22. `defineExpose`
23. `provide`

Do not create empty groups. Preserve relative order when it affects behavior.

## Formatting Style

Prefer `function` declarations for component-owned functions when conversion is safe. Do not convert arrows used as callbacks, arrows relying on lexical `this`, reassigned functions, functions with properties, overload-sensitive code, or cases where hoisting/inference could change behavior.

Use explicit blocks in formatted computeds:

```ts
const normalizedName = computed(() => {
  if (!name.value) {
    return '';
  }

  return name.value.trim();
});
```

Writable computeds must use method syntax with `get` before `set`:

```ts
const modelValue = computed({
  get() {
    return value.value;
  },
  set(newValue) {
    value.value = newValue;
  },
});
```

Keep stores adjacent to their `storeToRefs` calls with no blank line inside the unit:

```ts
const userStore = useUserStore();
const {
  user,
  loading,
} = storeToRefs(userStore);
```

## Validation Output

Report outcomes clearly:

```text
Formatted: src/components/UserForm.vue
Unchanged: src/components/UserForm.vue
Conversion required: src/components/LegacyComponent.vue
Formatted with warnings: src/components/UserForm.vue
```

Include warnings for declarations kept in place, unsafe conversions skipped, unresolved `this` access, or store/storeToRefs relationships that could not be proven safe.
