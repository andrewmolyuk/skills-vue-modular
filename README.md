# skills-vue-modular

Collection of agent skills focused on Vue modular architecture workflows.

## Included Skills

| Skill | Purpose |
| --- | --- |
| `eslint-plugin-vue-modular` | Configure and troubleshoot `eslint-plugin-vue-modular` with ESLint v9 flat config, project settings, migration strategy, and common rule fixes. |

## Install

Install from GitHub using `npx skills add` with interactive skill selection

```bash
npx skills add andrewmolyuk/skills-vue-modular
```

Install a specific skill directly

```bash
npx skills add andrewmolyuk/skills-vue-modular --skill eslint-plugin-vue-modular
```

### Verify Installation

```bash
npx skills list
```

## Repository Structure

```text
skills/
  eslint-plugin-vue-modular/
    SKILL.md
```

## Quick Start

### Use This Repository as a Reference

1. Open `skills/eslint-plugin-vue-modular/SKILL.md`.
2. Reuse the install/config snippets in your Vue project.
3. Apply the migration checklist and fix patterns while addressing lint violations.

### Use in a Skills-Compatible Agent Setup

Place this repository's `skills/` directory into your agent skills location, or copy the specific skill folder:

- `skills/eslint-plugin-vue-modular/`

After installation, invoke the skill when you need help with:

- ESLint v9 flat config for Vue modular projects
- `settings['vue-modular']` path and alias customization
- Architecture-boundary lint violations and rule tuning

## Skill Details

The `eslint-plugin-vue-modular` skill includes:

- Installation command for the plugin
- Minimal and advanced flat-config examples
- Supported rule-area overview
- Common violation remediation patterns
- Incremental migration guidance (`recommended` to `all`)

## Source

This skill is based on:

- https://github.com/andrewmolyuk/eslint-plugin-vue-modular

## License

See `LICENSE.md`.
