# Beslogic shared configurations

These are the base configurations shared across different Beslogic projects.

We recommend keeping the changes to the minimum needed (for example, avoid re-organizing entire sections) as to minimize the diff the next time you update your configs. It is generally recommended to check back on this page when updating workspace/editor/dev tooling settings.

## VSCode

Copy the [.vscode folder](/.vscode) into the root of your project(s) workspace(s).\
If using a devcontainer or a code-workspace, copy its content in the appropriate sections instead.

Read the comments carefully, you will likely need to uncomment or modify a thing a two for your project. You can trim out configuration sections and extensions for languages your project doesn't use.

## EditorConfig

Copy the [.editorconfig skeleton file](/.editorconfig) into the root of your git project (even if you have multiple workspaces).

## dprint

Add the following into your `.dprint.jsonc` at the root of your git project (even if you have multiple workspaces):

```jsonc
{
  // https://dprint.dev/config/#extending-a-different-configuration-file
  "extends": "https://raw.githubusercontent.com/BesLogic/shared-configs/<hash>/.dprint.jsonc"
}
```

This configuration automatically includes known "exclusion" folders. But if you need to add more, you can copy and expand the `excludes` list in [.dprint.jsonc](/.dprint.jsonc).\
It is recommended to target a specific commit hash to pin your config version, otherwise, you may deal with unexpected changes in formatting.

### package.json

#### (for dprint and ESLint)

To run dprint along with ESLint, add the following to your `package.json`:

```json
"scripts": {
  "eslint": "eslint ./ --ignore-path .gitignore",
  "lint": "dprint check && npm run eslint",
  "lint:fix": "npm run eslint -- --fix && dprint fmt",
}
```

If your `package.json` isn't at root (like frontend/backend split monorepos), you can do the following to ensure the formatter (dprint) is run on _all_ files:

```json
"scripts": {
  "eslint": "eslint ./ --ignore-path .gitignore",
  "lint": "cd .. && dprint check && cd frontend && npm run eslint",
  "lint:fix": "npm run eslint -- --fix && cd .. && dprint fmt",
}
```

## TypeScript (tsconfig) and ESLint

See <https://www.npmjs.com/package/eslint-config-beslogic#base-tsconfigjson>

## Python

### Ruff

See <https://github.com/BesLogic/Beslogic-Ruff-Config>. Make sure to read the [Additional configuration](https://github.com/BesLogic/Beslogic-Ruff-Config/blob/main/README.md#additional-configuration) section.

### mypy

Copy the [mypy.ini](/mypy.ini) into the root of your python project.\
(this may change if/when mypy allows sharing external configs: <https://github.com/python/mypy/issues/9620>)

You can remove sections that don't concern you (like pyright infered return types or specific plugins). Add any config specific to your project under `Specific to this project`.

## PR Autofixes

We've stopped attempting to support pre-commit.ci due to restrictions on downloaded bundle size and runtime downloads on the free tier. As well as having to individually duplicate and pin each ESLint plugin to an exact version. And finally, issues typescript-eslint project configuration and going Out Of Memory.

Instead, we can recommend:

- <https://autofix.ci/> (GitHub App, free for public repos)
- <https://github.com/marketplace/actions/add-commit> (GitHub Action, needs a custom PAT with `Contents` access to re-trigger workflows, cautious of infinite loops!)
- <https://github.com/marketplace/actions/git-auto-commit> (IDEM)
- <https://marketplace.visualstudio.com/items?itemName=Beslogic.apply-git-changes> (Private extension for Azure DevOps, we may publish one day)

<details>
<summary>GitHub Actions example configuration</summary>

```yaml
name: PR Autofixes

on:
  pull_request:
    branches:
      - main

jobs:
  Autofixes:
    runs-on: ubuntu-latest
    # Only run autofixes on PRs
    if: ${{ github.event_name == 'pull_request' }}
    steps:
      - uses: actions/checkout@v4
        with:
          # Checkout the fork/head-repository and push changes to the fork.
          # If you skip this, the base repository will be checked out and changes will be committed to the base repository!
          repository: ${{ github.event.pull_request.head.repo.full_name }}
          # Checkout the branch made in the fork. Will automatically push changes back to this branch.
          ref: ${{ github.event.pull_request.head.ref }}
          # Token with Contents permissions to allow retriggering workflow
          token: ${{ secrets.PR_AUTOFIX_PAT }}

      - uses: actions/setup-node@v4

      - run: npm ci

      # - uses: other_custom_steps

      # Ruff autofixes
      - uses: astral-sh/ruff-action@v1
        # Fix even on other steps failure
        if: ${{ !cancelled() }}
        with:
          args: check --fix
      - uses: astral-sh/ruff-action@v1
        # Fix even on other steps failure
        if: ${{ !cancelled() }}
        with:
          args: format

      # ESLint & Dprint autofixes
      - run: npm run lint:fix
        # Fix even on other steps failure
        if: ${{ !cancelled() }}

      # Using https://github.com/marketplace/actions/add-commit
      - name: Commit autofixes
        uses: EndBug/add-and-commit@v9
        # TODO: Prevent infinite loops, github.event.head_commit.author.name is not accessible in this context
        # if: ${{ github.event.head_commit.author.name != 'github-actions' }}
        # Push autofixes even on failure
        if: ${{ !cancelled() }}
        with:
          default_author: github_actions

      # OR using https://github.com/marketplace/actions/git-auto-commit
      - name: Commit autofixes
        uses: stefanzweifel/git-auto-commit-action@v5
        # TODO: Prevent infinite loops, github.event.head_commit.author.name is not accessible in this context
        # if: ${{ github.event.head_commit.author.name != 'github-actions' }}
        # Push autofixes even on failure
        if: ${{ !cancelled() }}
```

</details>
