---
name: vue-modular-project-structure
description: Consolidated guidance for structuring Vue 3 modular projects. Combines the repository conventions and the Vue3 modules approach into a single, actionable reference for design, enforcement, and migration.
---

# Vue Modular Project Structure

This skill consolidates the web project conventions and the Vue3 project modules approach to help design, enforce, and migrate to and keep a modular Vue 3 architecture.

## When to Use This Skill

- Starting a new Vue 3 application that must scale across domains and teams.
- Migrating a legacy codebase toward module-level encapsulation and public APIs.
- Designing folder/layout conventions, public API exports, and routing auto-discovery.
- Configuring ESLint or automation to enforce module boundaries and naming rules.

## Key Concepts

- Module = domain folder with views, components, services, composables, stores, types and a mandatory `index.ts` public API.
- Global assets = `components/`, `composables/`, `features/`, `entities/`, and `shared/` (UI kit + utilities).
- App infrastructure = `app/` (router, plugins, layouts, global styles).
- Layered access: the repository defines six layers; imports are allowed only downward.

## Example Project Structure

```plaintext
src/
├── app/            # router, plugins, layouts, styles
├── components/     # global business components
├── composables/    # global reusable composables
├── services/       # global API clients and services
├── entities/       # shared domain entities
├── stores/         # global Pinia stores
├── features/       # cross-cutting features (must have index.ts)
├── modules/        # domain modules (each must have index.ts)
└── shared/         # UI kit + pure utilities
```

## Module Structure (required public API)

Each `src/modules/<feature>/` MUST follow this pattern. Only `index.ts` is public – everything else is private to the module.

```
src/modules/<feature>/
  views/
  components/
  api/            # module-private API/business logic
  features/       # module-local sub-features
  types/
  index.ts        # PUBLIC API (MANDATORY)
```

Example `index.ts` public API:

```ts
// modules/auth/index.ts
export { default as moduleRoutes } from './routes'
export { default as moduleNavigation } from './menu'
export { Login } from './composables/login'
export type { AuthConfig } from './types'
```

## Layer Access Rules (summary)

Six layers (top → bottom). Layers may only import from layers below them.

- Infrastructure (`app/`): may import any lower layer but must access modules only via `index.ts`.
- Domain (`modules/`, `features/`): may import Shared Business, State, Data, Utility; no cross-module imports.
- Shared Business (`composables/`, `components/`, `services/`): can cross-reference each other and import lower layers.
- State (`stores/`): can import Data and Utility.
- Data (`entities/`): can import Utility and other entities.
- Utility (`shared/`): self-contained (no imports from other layers).

Use ESLint with `eslint-plugin-vue-modular` or equivalent rules to enforce these boundaries programmatically.

## Store Organization

- Two-tier approach: global `stores/` for shared state; module-local `modules/*/stores/` for module-scoped state.
- App-level or root stores are stable APIs and should change conservatively.

## Routing Registration (Patterns)

Three robust solutions for registering module routes without violating layer rules:

1. Auto-discovery (recommended): use Vite's `import.meta.glob` to discover `/src/modules/*/index.{ts,js}` and read exported `moduleRoutes`.

2. Configuration-based: maintain an `app/config/modules.ts` listing enabled modules and load their public APIs dynamically with `import()`.

3. Build-time generation: use a Vite plugin to scan modules and generate a `generated-routes.ts` file at build time.

Example (auto-discovery):

```ts
// app/router/auto-discovery.ts
const moduleApis = import.meta.glob('/src/modules/*/index.{ts,js}')
for (const path in moduleApis) {
  const mod = await moduleApis[path]()
  if (mod.moduleRoutes) routes.push(...mod.moduleRoutes)
}
```

## Naming Conventions & SFC Order

- Components, Pages: `PascalCase`.
- Views: `PascalCase` + `View` suffix (e.g., `LoginView.vue`).
- Files/folders: `kebab-case`.
- Composables/services: `camelCase`.
- Interfaces/types/enums: `PascalCase`.
- SFC order: `<template>` → `<script>` → `<style>` (style last).

## Testing Exceptions

- Test files get special exceptions: `.test.`, `.spec.`, `tests/`, `__tests__/` are allowed to import internal module files for unit/integration testing.

## Style Isolation & Tailwind

- Modules should avoid importing global app SCSS directly; use scoped styles, module-local variables, or CSS custom properties.
- Tailwind works naturally with modular architecture; configure `content` to scan `src/**/*.{vue,ts,js}` so utilities are available across modules.

## Where to Put Code (Quick Rules)

- Basic UI primitives → `shared/ui/`.
- Global business components → `components/`.
- Cross-cutting features → `features/` (must export `index.ts`).
- Module-specific functionality → `modules/<name>/`.
- Pure domain types → `entities/`.

## Enforcing the Rules

- Use `eslint-plugin-vue-modular` (or your project linter config) to:
  - Disallow imports that bypass `index.ts` public APIs.
  - Enforce naming and file conventions.
  - Allow test-file exceptions automatically.

Example ESLint enforcement snippet:

```js
// eslint.config.mjs
import vueModular from 'eslint-plugin-vue-modular'
export default [
  ...vueModular.configs.recommended,
  {
    files: ['src/**/*.{ts,vue}'],
    plugins: { 'vue-modular': vueModular },
    settings: {
      'vue-modular': { rootPath: 'src', rootAlias: '@', featuresPath: 'src/features', modulesPath: 'src/modules' }
    }
  }
]
```

## Migration Tips

1. Start with conventions and documentation; pick a single module to migrate as a pilot.
2. Introduce `index.ts` public APIs for existing modules and migrate app imports to those APIs.
3. Add lint rules in `recommended` mode first; fix violations iteratively.
4. Prefer moving shared logic to `composables/`, `features/`, or `shared/` rather than creating cross-module imports.

## Validation Checklist

- [ ] Each module has `index.ts` public API and app imports use it.
- [ ] Layer access rules are codified in ESLint settings and return no violations.
- [ ] Routing registration uses auto-discovery / config / build-time generation.
- [ ] Naming conventions and SFC order are followed.
- [ ] Global UI kit lives under `shared/ui/` and is used by business components.
- [ ] Tests remain allowed to import internals; production code does not.

## Implementation Notes

- Module exports should be small, well-documented, and stable.
- Document any permitted exceptions (with reasons) in the module `README.md` and allow ESLint rule-level ignores where absolutely necessary.
- Regularly review module boundaries and public APIs as the project evolves to prevent leakage and maintain encapsulation.
