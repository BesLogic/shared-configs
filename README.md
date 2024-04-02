# Beslogic shared configurations

These are the base configurations shared across different Beslogic projects.

## .editorconfig

Copy the [skeleton file](/.editorconfig) into the root of your project.

## dprint

Add the following into your `dprint.json`:
```jsonc
{
  // https://dprint.dev/config/#extending-a-different-configuration-file
  "extends": "https://raw.githubusercontent.com/BesLogic/shared-configs/main/dprint.json"
}
```
This configuration automatically includes known "exclusion" folders. But if you need to add more, you can copy and expand the `exludes` list in [dprint.json](/dprint.json).

## TypeScript and ESLint

See https://www.npmjs.com/package/eslint-config-beslogic#user-content-parallel-configurations
