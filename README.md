# Beslogic shared configurations

These are the base configurations shared across different Beslogic projects.

We recommend keeping the changes to the minimum needed (for example, avoid re-organizing entire sections) as to minimize the diff the next time you update your configs. It is generally recommended to check back on this page when updating workspace/editor/dev tooling settings.

## VSCode

Copy the [.vscode folder](/.vscode) into the root of your git project.\
If using a devcontainer or a code-workspace, copy its content in the appropriate sections instead.

Read the comments carefully, you will likely need to uncomment or modify a thing a two for your project. You can trim out configuration sections and extensions for languages your project doesn't use.

## EditorConfig

Copy the [.editorconfig skeleton file](/.editorconfig) into the root of your git project.

## dprint

Add the following into your `.dprint.jsonc`:

```jsonc
{
  // https://dprint.dev/config/#extending-a-different-configuration-file
  "extends": "https://raw.githubusercontent.com/BesLogic/shared-configs/main/dprint.json"
}
```

This configuration automatically includes known "exclusion" folders. But if you need to add more, you can copy and expand the `exludes` list in [dprint.json](/dprint.json).\
If you'd like to pin your config version, you can use a commit hash, like `https://raw.githubusercontent.com/BesLogic/shared-configs/a0d9a34/dprint.json`.

To run dprint along with ESLint, add the following to your `package.json`:

```json
"scripts": {
  "eslint": "eslint ./ --ignore-path .gitignore",
  "lint": "dprint check && npm run eslint",
  "lint:fix": "dprint fmt && npm run eslint -- --fix",
}
```

If your `package.json` isn't at root (like frontend/backend split monorepos), you can do:

```json
"scripts": {
  "eslint": "eslint ./ --ignore-path .gitignore",
  "lint": "cd .. && dprint check && cd frontend && npm run eslint",
  "lint:fix": "cd .. && dprint fmt && cd frontend && npm run eslint -- --fix",
}
```

## TypeScript (tsconfig) and ESLint

See <https://www.npmjs.com/package/eslint-config-beslogic#user-content-parallel-configurations>

## Ruff

Copy the [ruff.toml skeleton file](/ruff.toml) into the root of your python project or root of your monorepo.\
(this may change if/when Ruff allows sharing external configs: <https://github.com/astral-sh/ruff/discussions/3363#discussioncomment-8911551> / <https://github.com/astral-sh/ruff/issues/12352>)
