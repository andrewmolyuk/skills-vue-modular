---
name: eslint-plugin-vue-modular
description: Helps configure eslint-plugin-vue-modular in Vue projects using ESLint v9 flat config, including plugin settings, architecture boundary rules, migration strategy, and violation fixes.
---

# ESLint Plugin Vue Modular

This skill helps set up and maintain `eslint-plugin-vue-modular` for modular Vue architecture.

## When to Use This Skill

Use this skill when the user:

- Asks to install or configure `eslint-plugin-vue-modular`
- Needs ESLint v9+ flat config examples for Vue projects
- Wants to configure `settings['vue-modular']` for custom paths or aliases
- Is migrating an existing codebase to modular architecture lint rules
- Needs help fixing boundary, naming, SFC, or index/public API rule violations

## What This Plugin Enforces

`eslint-plugin-vue-modular` enforces architecture boundaries and naming conventions for Vue modular projects.

Key areas include:

- Layer boundaries: `app-imports`, `feature-imports`, `shared-imports`
- Import style: `cross-imports-alias`, `internal-imports-relative`
- Public APIs: `feature-index-required`, `components-index-required`, `shared-ui-index-required`
- Naming and structure: `folder-kebab-case`, `file-component-naming`, `file-ts-naming`, `views-suffix`
- Vue SFC expectations: `sfc-required`, `sfc-order`
- Service/store conventions: `stores-location`, `store-filename-no-suffix`, `service-filename-no-suffix`

## How to Configure eslint-plugin-vue-modular

### Step 1: Confirm Prerequisites

Ensure the project uses ESLint v9+ with flat config (`eslint.config.js` or `eslint.config.mjs`).

### Step 2: Install the Plugin

```bash
npm install --save-dev eslint-plugin-vue-modular
```

ESLint is not bundled with the plugin, so install ESLint separately if needed.

### Step 3: Add a Minimal Flat Config

```js
import vueModular from 'eslint-plugin-vue-modular'

export default [...vueModular.configs.recommended]
```

### Step 4: Add Project Settings (Optional)

Use this when your source root, alias, or folder names differ from defaults.

```js
import vueModular from 'eslint-plugin-vue-modular'

export default [
  ...vueModular.configs.recommended,
  {
    files: ['**/*.ts', '**/*.tsx', '**/*.vue'],
    plugins: { 'vue-modular': vueModular },
    settings: {
      'vue-modular': {
        rootPath: 'src',
        rootAlias: '@',
        appPath: 'src/app',
        layoutsPath: 'src/app/layouts',
        featuresPath: 'src/features',
        sharedPath: 'src/shared',
        componentsFolderName: 'components',
        viewsFolderName: 'views',
        uiFolderName: 'ui'
      }
    }
  }
]
```

Notes:

- Provided `settings['vue-modular']` values are merged with plugin defaults.
- `file-ts-naming` supports rule-level `ignores`; defaults include `**/*.d.ts`, `**/*.spec.*`, `**/*.test.*`, `**/*.stories.*`.

### Step 5: Run Lint and Iterate

```bash
bunx eslint src/
```

Prefer `recommended` first for adoption, then move to `all` when the team is ready.

## How to Fix Common Violations

### Boundary Violations

- Move reusable logic to `shared/*`.
- Import through feature public APIs (`features/<feature>/index.ts`) instead of deep internals.

### Alias and Relative Import Warnings

- Use the configured root alias for cross-layer imports.
- Keep same-layer local imports relative unless path traversal becomes too deep.

### Missing Public API / Index Rules

- Add `index.ts` where required and re-export intended public symbols.

### Naming and Suffix Rules

- Rename files and folders to match enforced conventions before adding exceptions.

## Migration Tips

1. Start with `vueModular.configs.recommended`.
2. Fix structural and naming violations first.
3. Add narrow, documented exceptions only when unavoidable.
4. Move to `vueModular.configs.all` after stabilization.

## Validation Checklist

- `eslint-plugin-vue-modular` is installed in dev dependencies.
- ESLint config uses flat config and imports plugin correctly.
- Project path settings match actual folder layout.
- Lint command runs successfully and reports expected architectural issues.
