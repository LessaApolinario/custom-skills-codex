# Vue SFC Formatting Rules

Load this reference before formatting `.vue` files with `$format-sfc-vuejs`.

## Priorities

1. Preserve behavior.
2. Keep transformations safe.
3. Improve readability.
4. Standardize visual order.

When these conflict, preserve behavior, keep the declaration in its required position, emit a warning, and continue with safe formatting.

## Global Formatting

- Use 2 spaces for JavaScript, TypeScript, template, CSS, SCSS, Less, object literals, arrays, functions, callbacks, generics, multiline parameters, multiline arguments, interfaces, enums, hooks, watchers, and imports.
- Do not use tabs.
- Remove trailing whitespace.
- Collapse repeated blank lines unless a block-specific rule requires spacing.
- Use semicolons in JavaScript and TypeScript.
- Replace indentation tabs only when they are indentation, not literal content in strings, template literals, or value-sensitive blocks.

## SFC Blocks

Order blocks as scripts, `template`, styles, then custom blocks. Preserve all attributes on existing blocks, including `lang`, `scoped`, `module`, and `src`. Do not add or remove `scoped` on existing styles. Do not merge blocks. Preserve multiple style blocks and coexisting traditional/setup scripts in their respective relative order.

Require a script, a template, and at least one style block even when unused. Do not remove existing blocks. Add only absent block kinds without placeholder content. Empty script, template, and style blocks must have opening and closing tags on the same line, including existing whitespace-only blocks. Preserve attributes and use paired tags, not self-closing tags. Blocks containing code, markup, or comments are not empty and remain multiline:

For JavaScript:

```vue
<script setup></script>

<template></template>

<style scoped></style>
```

For TypeScript:

```vue
<script setup lang="ts"></script>

<template></template>

<style scoped></style>
```

Use exactly one blank line between blocks. Choose the language for a missing script from explicit user instructions, then the project's Vue SFC language convention (inspect nearby components and project configuration); default to JavaScript if no convention can be established. Create `<script setup lang="ts">` for TypeScript and `<script setup>` without `lang` for JavaScript. Always create a missing style as `<style scoped>`. Existing script languages and style attributes remain unchanged. Any existing script satisfies the script requirement; do not add setup alongside a traditional script just to fill the requirement. Files requiring traditional-script conversion still follow the authorization/skip workflow before writing. Template/style formatting opt-outs skip content formatting, not creation of missing required blocks.

## Imports

Keep all imports at the top. Prefer this order:

1. Vue
2. Vue ecosystem
3. external libraries
4. project aliases
5. relative paths
6. type imports
7. styles
8. assets
9. side-effect imports

Rules:

- Preserve paths, aliases, `import type`, import attributes, comments, and side-effect imports.
- Do not remove imports automatically.
- Do not merge imports when comments would be displaced.
- Do not add imports when the project uses auto imports.
- Detect likely auto imports from Nuxt, Quasar, `unplugin-auto-import`, `auto-imports.d.ts`, or Vite configuration.

## Types

Place interfaces, types, enums, namespaces, and type-only declarations after imports and before Vue macros. Preserve TypeScript features such as generics, assertions, `satisfies`, `as const`, overloads, decorators, typed props, typed emits, typed slots, typed refs, and typed injects.

### Object Generic Interfaces

In TypeScript scripts, replace explicit inline object literal generic types with named interfaces. Apply to `defineProps`, including `withDefaults(defineProps())`, `defineEmits`, `defineExpose`, `defineSlots`, `defineModel`, and Vue APIs accepting such generics, including `ref`, `shallowRef`, `reactive`, `shallowReactive`, `computed`, `inject`, and `provide`. Recognize imported aliases by their Vue origin; do not apply the rule to unrelated functions sharing these names.

- Place extracted interfaces in the type declaration group after imports. Keep local generic parameters in scope: parameterize the interface and pass the original type parameters when safe; otherwise preserve the type and warn.
- Prefer `Props`, `Emits`, `Exposed`, and `Slots` for their macros. For other calls use descriptive PascalCase names derived from the value or purpose, such as `UserState` for `user`. Reuse an equivalent interface already in scope. On a name collision, qualify with the component/value name; use a numeric suffix only if still necessary. Never merge with an unrelated existing declaration.
- Preserve optional/readonly modifiers, index and call signatures, overload order, tuple labels, comments, and member types. Extract the object shape at the generic boundary; nested member shapes may remain inline inside the interface.
- Keep existing named types and aliases unchanged. Do not invent generics for inferred calls, convert runtime declarations to type declarations, change runtime arguments/defaults, or migrate JavaScript to TypeScript.
- Preserve unions, intersections, arrays, tuples, mapped types, and conditional types as compositions. Extract compatible object literal constituents only, e.g. `ref<{ id: string } | null>(null)` becomes `ref<UserState | null>(null)` with `interface UserState { id: string; }`. Do not wrap a union or mapped type in an invalid interface, flatten a composition, or extract mapped members as interface members.
- Preserve type-checking semantics and the installed Vue compiler's type-to-runtime support. If extraction cannot be proven safe, retain the original type and report a warning. For `defineProps`/`defineEmits`, check compiler compatibility rather than assuming all TypeScript types can be resolved. See the [official Vue macro documentation](https://vuejs.org/api/sfc-script-setup.html#type-only-props-emit-declarations).

Props with defaults, before:

```ts
const props = withDefaults(defineProps<{
  readonly title?: string;
}>(), { title: 'Users' });
```

After:

```ts
interface Props {
  readonly title?: string;
}

const props = withDefaults(defineProps<Props>(), { title: 'Users' });
```

Emits, before:

```ts
const emit = defineEmits<{
  save: [id: number];
  cancel: [];
}>();
```

After:

```ts
interface Emits {
  save: [id: number];
  cancel: [];
}

const emit = defineEmits<Emits>();
```

If the original emits type uses call signatures such as `(event: 'save', id: number): void;`, copy those signatures into `Emits` unchanged rather than converting them to tuple syntax.

Expose, before (with an existing `reset` function):

```ts
defineExpose<{
  reset: () => void;
}>({ reset });
```

After (interface in the type group; call remains after watchers):

```ts
interface Exposed {
  reset: () => void;
}

// Other component declarations remain in their required positions.
defineExpose<Exposed>({ reset });
```

Reactivity, before:

```ts
const user = ref<{
  id: string;
} | null>(null);
```

After:

```ts
interface UserState {
  id: string;
}

const user = ref<UserState | null>(null);
```

## Vue Macros

Order macros as:

1. `defineOptions`
2. `defineSlots`
3. `defineEmits`
4. `defineProps`
5. `defineModel`

Treat `withDefaults(defineProps())` as `defineProps`.

## Variables

Place `let` variables before ordinary `const` variables. Split multiple declarations:

```ts
let page = 1;
let search = '';
```

Do not classify these as ordinary consts:

- Vue macros
- `useRoute` or `useRouter`
- composables
- stores
- `storeToRefs`
- `inject`
- `ref` and related APIs
- `reactive` and related APIs
- `toRef` and `toRefs`
- `computed`

Simple scalar variables can stay consecutive. Complex objects and arrays may be separated with a blank line for readability.

## Vue Router

Place `useRoute()` and `useRouter()` after variables and before composables:

```ts
const route = useRoute();
const router = useRouter();
```

Classify by the called function, not the variable name. Recognize aliases such as `const currentRoute = useRoute();`. Do not move calls inside functions. Do not move router navigations or calls with side effects.

## Composables

Place top-level `useSomething()` calls after Vue Router and before stores. Treat `useI18n()` and `useQuasar()` as composables. Do not classify `useRoute`, `useRouter`, or stores as ordinary composables.

Preserve arguments, destructuring, relative order, and dependencies. If uncertain, keep the original position and warn.

## Pinia Stores

Identify stores by `useSomethingStore`, imports from store paths, later use in `storeToRefs`, or explicit project configuration.

Place stores after composables. Never classify stores as ordinary consts or ordinary composables.

Keep a store and its related direct destructuring and `storeToRefs` calls together:

```ts
const userStore = useUserStore();
const {
  loadUser,
  updateUser,
} = userStore;
const {
  user,
  loading,
} = storeToRefs(userStore);
```

Do not insert blank lines inside this unit. When multiple stores have `storeToRefs`, separate each unit with one blank line. Stores without `storeToRefs` may remain consecutive.

## Other Calls

Place independent safe top-level calls after stores and before injects, preserving relative order:

```ts
useHead({
  title: 'Users',
});

await loadInitialData();
```

Do not move unsafe or side-effect-sensitive calls. Keep top-level await behavior unchanged.

## Inject, Refs, Reactives, and ToRefs

Group `inject()` declarations after other safe calls.

Group refs after injects. Include `ref`, `shallowRef`, `customRef`, and configured equivalents.

Group reactives after refs. Include `reactive` and `shallowReactive`.

Group `toRefs` and `toRef` after reactives. Do not include `storeToRefs` here; it belongs with its store.

## Computeds

Place computeds after `toRefs`/`toRef` and before functions. Distinguish readonly and writable computeds.

Use explicit callback blocks and no implicit returns:

```ts
const normalizedName = computed(() => {
  return name.value.trim();
});
```

Use a blank line between logical blocks and before the final return when prior code exists:

```ts
const fullName = computed(() => {
  const firstName = user.value?.firstName?.trim() ?? '';
  const lastName = user.value?.lastName?.trim() ?? '';

  if (!firstName && !lastName) {
    return '';
  }

  return `${firstName} ${lastName}`.trim();
});
```

Avoid single-line conditional returns:

```ts
if (!name.value) {
  return '';
}
```

When safe, expand callbacks inside formatted computeds for `map`, `filter`, `find`, `some`, `every`, `reduce`, `sort`, and similar utilities:

```ts
return users.value.map((user) => {
  return user.name;
});
```

Do not rewrite callback expressions when doing so could alter types or behavior.

Writable computeds must use object form with method syntax:

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

Rules:

- `get` before `set`.
- No arrow functions for `get` or `set`.
- No one-line `get` or `set`.
- Preserve types, comments, and logic.
- Do not add unnecessary returns to setters.

## Functions

Place component-owned functions after computeds.

Prefer declaration syntax when safe:

```ts
function handleCreate() {
  if (!name.value) {
    return;
  }

  return name.value;
}
```

Safe arrow conversion example:

```ts
const normalizeName = (name: string): string => {
  return name.trim();
};
```

becomes:

```ts
function normalizeName(name: string): string {
  return name.trim();
}
```

Do not convert arrows that depend on lexical `this`, are callbacks, are nested, are reassigned, have properties, affect hoisting, alter inference, involve overloads, or are otherwise risky.

## Hooks and Watchers

Place hooks after functions and preserve relative order:

- `onBeforeMount`
- `onMounted`
- `onBeforeUpdate`
- `onUpdated`
- `onBeforeUnmount`
- `onUnmounted`
- `onActivated`
- `onDeactivated`
- `onErrorCaptured`
- `onRenderTracked`
- `onRenderTriggered`
- `onServerPrefetch`

Place watchers after hooks and preserve relative order:

- `watch`
- `watchEffect`
- `watchPostEffect`
- `watchSyncEffect`

## DefineExpose and Provide

Place `defineExpose` after watchers.

Place `provide` at the end of `script setup`, preserving relative order.

## Comments

Keep comments attached to the declaration or block they describe. Preserve JSDoc, TODO, FIXME, eslint, prettier, TypeScript directive comments, import comments, and business comments.

Do not move a comment to a different declaration.

## Initialization Dependencies

Analyze dependencies before moving declarations. Do not move a declaration above values it reads during initialization. Preserve relative order for composables, stores, hooks, watchers, provides, subscriptions, timers, event registrations, API calls, and side effects.

Warn:

```text
Some declarations were not moved because of initialization dependencies.
```

## Template

Use 2-space indentation. It is safe to remove trailing whitespace, normalize blank lines, and format multiline attributes conservatively.

Do not alter directives, expressions, text, events, slots, `v-if`, `v-for`, `v-model`, element order, component names, or attribute order unless explicitly requested.

Template refs may be converted from `$refs` only when safe. If the type cannot be inferred, add a TODO and warning.

## Style

Use 2-space indentation in CSS, SCSS, Less, and other style languages.

Preserve language, scope, modules, selectors, properties, values, variables, mixins, imports, comments, media queries, and keyframes.

Do not reorder properties, alter selectors, alter values, convert units, add/remove `scoped` on existing style blocks, convert SCSS to CSS, or rename classes. When no style block exists, create an empty `<style scoped>` as required by the SFC Blocks section.

## Traditional Script Conversion

Convert traditional `script` only with explicit authorization.

When safe, map:

- `props` to `defineProps`
- `emits` to `defineEmits`
- `data` to `ref` or `reactive`
- `computed` to `computed`
- `methods` to functions
- `watch` to `watch`
- `inject` to `inject`
- `provide` to `provide`
- `expose` to `defineExpose`
- `components` to direct imports/usage where needed
- lifecycle hooks to Composition API hooks

Lifecycle mapping:

- `beforeMount` to `onBeforeMount`
- `mounted` to `onMounted`
- `beforeUpdate` to `onBeforeUpdate`
- `updated` to `onUpdated`
- `beforeUnmount` to `onBeforeUnmount`
- `unmounted` to `onUnmounted`
- `activated` to `onActivated`
- `deactivated` to `onDeactivated`
- `errorCaptured` to `onErrorCaptured`
- `serverPrefetch` to `onServerPrefetch`

Analyze `created` and `beforeCreate` individually.

Warn before partial conversion for mixins, extends, decorators, global `this` properties, `$refs`, `$attrs`, `$slots`, `$parent`, `$root`, `$listeners`, `$route`, `$router`, `$store`, `$t`, `$i18n`, `$nextTick`, Vue 2 filters/APIs, dynamic components, dynamic property names, plugin custom options, complex `this` logic, and hard-to-map side effects.

Mark uncertain conversions:

```ts
// TODO(format-sfc-vuejs): review conversion of this.$api
```

## Framework Notes

Nuxt: preserve `useAsyncData`, `useFetch`, `useState`, `useHead`, `definePageMeta`, `defineNuxtRouteMiddleware`, top-level await, and auto imports. Do not move macros or effectful calls without analysis.

Quasar: preserve `useQuasar`, `Notify`, `Dialog`, `Loading`, boot files, auto imports, and Quasar types. Treat `useQuasar()` as a composable.

Vue Router: preserve route/router declarations and do not move navigations.

Pinia: preserve `storeToRefs`, `$patch`, `$subscribe`, `$onAction`, actions, getters, and dependent stores.

Internationalization: treat `useI18n()` as a composable.

## Optional CLI Semantics

If implementing or following command-style behavior, support these semantics:

- `--check`: report whether changes are needed without writing.
- `--write`: write validated changes.
- `--dry-run`: do not save; show output or diff.
- `--stdin`: read source from stdin.
- `--convert-script-setup`: authorize safe conversions.
- `--allow-partial-conversion`: authorize partial conversions and require `--convert-script-setup`.
- `--no-format-template`: skip template formatting.
- `--no-format-style`: skip style formatting.
- `--no-convert-arrow-functions`: skip arrow-to-function conversion.
- `--verbose`: show classifications and decisions.
- `--backup`: create `.bak` before writing.

## Configuration

Recognize `format-sfc-vuejs.config.json` when present. Defaults:

```json
{
  "indentSize": 2,
  "useTabs": false,
  "useSemicolons": true,
  "sortImports": true,
  "preserveImportGroups": false,
  "convertSafeArrowFunctions": true,
  "formatTemplate": true,
  "formatStyles": true,
  "dependencySafety": "strict",
  "unknownStatementsPosition": "after-stores",
  "convertScriptSetup": false,
  "allowPartialConversion": false
}
```

Do not change the 2-space/no-tabs default unless the user explicitly requests a project-specific override.

## Validation Checklist

After formatting:

1. Parse the SFC again.
2. Validate JavaScript or TypeScript syntax.
3. Validate template syntax when tooling is available.
4. Validate style syntax when tooling is available.
5. Confirm block closing tags and attributes, the presence of script/template/style even when empty, and preservation of existing script/style multiplicity and order. New scripts must use `setup` and the appropriate TypeScript/JavaScript language form; newly created styles must have `scoped`.
6. Confirm empty script/template/style blocks have paired tags on a single line, exactly one blank line separates blocks, and no declaration was lost.
7. Confirm imports are preserved.
8. Confirm store and `storeToRefs` adjacency.
9. Confirm 2-space indentation and no tabs.
10. Confirm eligible explicit inline object generic types use interfaces without changing member types, compositions, defaults, arguments, or inference; confirm collision-free names and Vue compiler compatibility. Preserve JavaScript.
11. Confirm idempotence when practical: `format(format(source)) === format(source)`, including no duplicate interfaces or required blocks on the second pass.
12. Stop writing if validation fails.

For `--write`, write to a temporary file, validate the temporary file, then replace the original only after success. With `--backup`, create `Component.vue.bak`.

## Expected Reports

```text
Formatted: src/components/UserForm.vue
Converted and formatted: src/components/LegacyComponent.vue
Unchanged: src/components/UserForm.vue
Conversion required: src/components/LegacyComponent.vue
Formatted with warnings: src/components/UserForm.vue
```
