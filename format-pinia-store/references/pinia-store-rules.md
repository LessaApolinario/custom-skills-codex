# Pinia Store Rules

## Language

Detect JavaScript or TypeScript automatically from file extensions, `tsconfig.json`, `jsconfig.json`, dependencies, existing stores, bundler configuration, and linter configuration.

For a clearly JavaScript project:

- Use `domainStore.js`.
- Do not use TypeScript, interfaces, types, generics, casts, JSDoc, or comments that simulate types.

For a clearly TypeScript project:

- Use `domainStore.ts`.
- Use TypeScript for state, refs, parameters, models, optional values, and returns when genuinely needed.

For a mixed or ambiguous project, ask which language to use. Do not choose arbitrarily.

## Format

When creating a new store, ask:

```text
Which store format should be used?

1. Option Store
2. Setup Store
```

When working on an existing store, preserve its current format. Never convert automatically. If conversion would be useful, ask first:

```text
The current store uses Option Store. Do you want to:

1. Keep it as Option Store
2. Convert it to Setup Store
```

or:

```text
The current store uses Setup Store. Do you want to:

1. Keep it as Setup Store
2. Convert it to Option Store
```

## Names

The file name, composable name, and ID must represent the same domain.

For `product`:

```text
File: productStore.ts
Composable: useProductStore
ID: product
```

For `companyOpportunity`:

```text
File: companyOpportunityStore.ts
Composable: useCompanyOpportunityStore
ID: companyOpportunity
```

When renaming a store:

1. Rename the file.
2. Rename the composable.
3. Fix the `defineStore` ID.
4. Locate old imports.
5. Update affected imports while preserving aliases.
6. Verify that no old references remain.

## Formatting

- Respect ESLint and Prettier.
- If no identifiable configuration exists, use semicolons.
- Never mix styles in the same file.
- Use explicit blocks:

```ts
if (condition) {
  return;
}
```

- Avoid inline returns:

```ts
const getName = () => {
  return name;
};
```

- In Option Stores, use an explicit state return:

```ts
state: (): ProductState => {
  return {
    products: [],
  };
},
```

- When logic appears before a return, separate the final return with a blank line.

## Imports

Create, remove, fix, or reorganize imports when needed to keep the store valid. Do not impose a custom import order if the project already has a formatter or linter for that. In TypeScript, `import type` is not mandatory; respect the project configuration.

## TypeScript

In a TypeScript Option Store, declare a state interface in the same file. The name must include the domain and must not be exported.

```ts
interface ProductState {
  products: Product[];
  selectedProduct?: Product;
  isLoadingProducts: boolean;
  productsError?: string;
}
```

The state type must come from the interface:

```ts
state: (): ProductState => {
  return {
    products: [],
    selectedProduct: undefined,
    isLoadingProducts: false,
    productsError: undefined,
  };
},
```

Do not use casts to type initial values:

```ts
products: [] as Product[]
```

For optional values, prefer optional properties and initialize explicitly when that clarifies the shape.

Do not type the `catch` variable:

```ts
catch (error) {
  console.error('Error in fetchProducts action: ', error);
}
```

Do not add explicit return types to getters by default. Use inference whenever possible. Add explicit return types only when required for compilation, such as when the Pinia version/documentation requires it because of `this` usage.

## Option Store

Use this order:

1. `state`
2. `getters`
3. `actions`

All three sections must exist. Inside `actions`, use section comments when there is content:

```ts
actions: {
  // mutations
  setProducts(newProducts: Product[]) {
    this.products = newProducts;
  },

  // actions
  async fetchProducts() {
    // ...
  },
},
```

Do not add redundant comments before `state`, `getters`, or `actions`.

### Getters

Every state property must have a corresponding getter. Getters must:

- Use the `get` prefix.
- Use PascalCase after the prefix.
- Use normal functions.
- Use explicit blocks.
- Avoid inline returns.
- Avoid TypeScript return annotations unless needed.

```ts
getters: {
  getProducts(state) {
    return state.products;
  },

  getSelectedProduct(state) {
    return state.selectedProduct;
  },

  getIsLoadingProducts(state) {
    return state.isLoadingProducts;
  },
},
```

### Mutations and setters

Every mutable state property must have a corresponding setter. Setters must use the `set` prefix, receive a parameter starting with `new`, and repeat the full property name.

```ts
setProducts(newProducts: Product[]) {
  this.products = newProducts;
}

setIsLoadingProducts(newIsLoadingProducts: boolean) {
  this.isLoadingProducts = newIsLoadingProducts;
}
```

Use the existing setter with `undefined` to clear optional values:

```ts
this.setSelectedProduct(undefined);
this.setProductsError(undefined);
```

Do not create redundant `clear...` methods unless the method already represents additional business behavior.

Business actions must not change state directly; they must call setters/mutations.

Specific array operations must have their own mutations when needed:

```ts
addProduct(newProduct: Product) {
  this.products.push(newProduct);
}

updateProduct(newProduct: Product) {
  const productIndex = this.products.findIndex((product) => {
    return product.id === newProduct.id;
  });

  if (productIndex === -1) {
    return;
  }

  this.products[productIndex] = newProduct;
}

removeProduct(productId: string) {
  this.products = this.products.filter((product) => {
    return product.id !== productId;
  });
}
```

These functions belong in `// mutations`.

## Setup Store

Use this order:

1. `// state`
2. `// getters`
3. `// mutations`
4. `// actions`
5. `return`

All sections must appear even when empty.

```ts
export const useProductStore = defineStore('product', () => {
  // state

  // getters

  // mutations

  // actions

  return {
    // state

    // getters

    // mutations

    // actions
  };
});
```

Always use `ref` for state. Do not use `reactive` to declare store state.

```ts
const products = ref<Product[]>([]);
const selectedProduct = ref<Product>();
const isLoadingProducts = ref(false);
```

Return every ref that represents state. Do not keep private state.

### Setup Store getters

Do not create a `computed` that only returns a ref directly:

```ts
const getProducts = computed(() => {
  return products.value;
});
```

Create getters only for derived data or to preserve an existing public API.

```ts
const getAvailableProducts = computed(() => {
  return products.value.filter((product) => {
    return product.isAvailable;
  });
});
```

Use explicit blocks in every `computed`.

If an existing store has a redundant computed that only returns `ref.value`, remove it only when no consumer depends on the name or when all usages can be updated safely. If removing it would break the public API, preserve it and report the redundancy.

### Setup Store mutations and actions

Mutations and actions must be declared functions:

```ts
function setProducts(newProducts: Product[]) {
  products.value = newProducts;
}

async function fetchProducts() {
  try {
    setIsLoadingProducts(true);

    const foundProducts = await productService.findAll();

    setProducts(foundProducts);
  } catch (error) {
    console.error('Error in fetchProducts action: ', error);
  } finally {
    setIsLoadingProducts(false);
  }
}
```

Do not use arrow functions for Setup Store mutations/actions.

Business actions must use mutations to change refs. Do not assign `products.value = ...` inside a business action.

The returned object must follow the section order:

```ts
return {
  // state
  products,
  selectedProduct,
  isLoadingProducts,

  // getters
  getAvailableProducts,

  // mutations
  setProducts,
  setSelectedProduct,
  setIsLoadingProducts,

  // actions
  fetchProducts,
};
```

## Async Actions

Async actions with external operations must use `try/catch/finally`.

External operations include HTTP, SDKs, Firebase, databases, storage, async services, and calls to async actions that may fail.

Use resource-specific loading state:

```ts
isLoadingProducts: false
```

Avoid generic loading when a store manages multiple resources. Do not create error state without an existing project pattern. When a pattern exists, reuse it.

```ts
async fetchProducts() {
  try {
    this.setIsLoadingProducts(true);
    this.setProductsError(undefined);

    const foundProducts = await productService.findAll();

    this.setProducts(foundProducts);
  } catch (error) {
    const errorMessage = handleApiError(error);

    this.setProductsError(errorMessage);
  } finally {
    this.setIsLoadingProducts(false);
  }
}
```

## Store Composition

Before using one store inside another:

1. Consult the official Pinia store composition documentation.
2. Check whether an inverse dependency already exists.
3. Avoid initialization cycles.
4. Avoid two Setup Stores directly reading each other's state during creation.
5. Prefer reads inside getters, computed values, or actions when that avoids a cycle.

In an Option Store, initializing another store inside an action is often valid:

```ts
async fetchProducts() {
  const authStore = useAuthStore();

  try {
    this.setIsLoadingProducts(true);

    const foundProducts = await productService.findAll(
      authStore.getAccessToken,
    );

    this.setProducts(foundProducts);
  } catch (error) {
    console.error('Error in fetchProducts action: ', error);
  } finally {
    this.setIsLoadingProducts(false);
  }
}
```

In a Setup Store, do not return another store from `return` just because it is used internally.

## Behavior Preservation

When formatting an existing store:

- Do not remove business rules.
- Do not change endpoints, payloads, or service contracts.
- Do not change public names without updating every consumer.
- Do not replace existing error handling with `console.error`.
- Do not remove or add plugins.
- Do not remove persistence configuration or add persistence.
- Do not change SSR behavior.
- Do not add HMR or remove existing HMR unnecessarily.
- Do not change tests except as needed to reflect authorized changes.
- Do not convert the store format without authorization.

If a rule in this skill would require a change that could break the store's public API, preserve compatibility or clearly report the conflict before finishing.

## Final Checklist

General:

- Official Pinia documentation was consulted.
- Pinia version was identified.
- Format was preserved or conversion was authorized.
- Project was identified as JavaScript or TypeScript.
- Linter and formatter were respected.
- Imports are valid.
- File follows `domainStore.js` or `domainStore.ts`.
- Composable follows `useDomainStore`.
- ID uses camel case.
- Imports affected by renaming were updated.

Option Store:

- Order is `state`, `getters`, `actions`.
- State uses an explicit return.
- TypeScript state interface includes the domain, is in the same file, and is not exported.
- No unnecessary casts exist in state.
- Every state property has a getter.
- Every mutable state property has a setter.
- Getters use `get`; setters use `set`; setter parameters use `new`.
- `// mutations` and `// actions` comments appear inside `actions`.
- Business actions use mutations to change state.

Setup Store:

- All state uses `ref`.
- `reactive` was not used for state.
- `// state`, `// getters`, `// mutations`, and `// actions` sections exist even when empty.
- Mutations and actions are declared functions.
- Derived computeds use explicit blocks.
- Redundant computeds were avoided or preserved for compatibility.
- All state refs are returned.
- Unnecessary internal dependencies are not returned.

Async and errors:

- External async actions use `try/catch/finally`.
- Loading is resource-specific.
- Loading is enabled before the operation and disabled in `finally`.
- The `catch` variable is not typed.
- Existing error handling was reused.
- `console.error` is used only when no pattern exists.
- No error state was created without an existing pattern.
