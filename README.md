# Beslogic shared configurations

These are the base configurations shared across different Beslogic projects.

## EditorConfig

Copy the [.editorconfig skeleton file](/.editorconfig) into the root of your git project.

## dprint

Add the following into your `dprint.json`:
```jsonc
{
  // https://dprint.dev/config/#extending-a-different-configuration-file
  "extends": "https://raw.githubusercontent.com/BesLogic/shared-configs/main/dprint.json"
}
```
This configuration automatically includes known "exclusion" folders. But if you need to add more, you can copy and expand the `exludes` list in [dprint.json](/dprint.json).  
If you'd like to pin your config version, you can use a commit hash, like `https://raw.githubusercontent.com/BesLogic/shared-configs/a0d9a34/dprint.json`.

## TypeScript (tsconfig) and ESLint

See https://www.npmjs.com/package/eslint-config-beslogic#user-content-parallel-configurations

## Ruff

Copy the [ruff.toml skeleton file](/ruff.toml) into the root of your python project or root of your monorepo.  
(this may change if/when Ruff allows sharing external configs: https://github.com/astral-sh/ruff/discussions/3363#discussioncomment-8911551 / https://github.com/astral-sh/ruff/issues/12352)
