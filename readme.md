# Prettier Auto Format Action

Formats a repo with Prettier. This action is useful for enforcing code style in a project. <!-- assuming that it works -->

## Usage

```yaml
format-code:
    uses: testsnake/prettier-auto-format-action/.github/workflows/auto-format-{packageManager}.yml@main # npm, pnpm, yarn are currently supported
    with:
      ref: ${{ github.ref }} # required, the branch or tag to format
      node-version: '20' # required, node version to use
      runs-on: ubuntu-latest # optional, default is ubuntu-latest
      commit-message: '[AUTO] chore: format code with prettier' # optional, default is '[AUTO] chore: format code with prettier'
      file-pattern: 'src/**/*.*' # optional, default is '**/*.*'
      fetch-depth: 0 # optional, default is 0
    secrets: inherit # required, inherit secrets from the workflow (to allow pushing to the repo)
```

## Examples

### Format repo on push to main
```yaml
name: Format On Push

on:
  push:
    branches:
      - main

jobs:
  format-code:
    uses: testsnake/prettier-auto-format-action/.github/workflows/auto-format-pnpm.yml@main # or auto-format-npm.yml, auto-format-yarn.yml
    with:
      ref: ${{ github.ref }}
      node-version: '20'
      runs-on: ubuntu-latest # optional, default is ubuntu-latest
      commit-message: '[AUTO] chore: format code with prettier' # optional, default is '[AUTO] chore: format code with prettier'
      file-pattern: 'src/**/*.*' # optional, default is '**/*.*'
      fetch-depth: 0 # optional, default is 0
    secrets: inherit
```

### Format repo on pull request
Formats entire repo only after a pull request to the `main` branch
```yaml
name: Format After PR Merge

on:
  pull_request:
    types: [closed]
    branches:
      - main

jobs:
  format-code:
    if: github.event.pull_request.merged == true
    uses: testsnake/prettier-auto-format-action/.github/workflows/auto-format-npm.yml@main # or auto-format-pnpm.yml, auto-format-yarn.yml
    with:
      ref: main
      node-version: '20'
      runs-on: ubuntu-latest
      commit-message: '[AUTO] format code'
    secrets: inherit
```

### Format changes pull request
Specifically only formats files modified in the pull request
Good for large repos
```yaml
name: Format Changes in PR

on:
  pull_request:
    types: [closed]
    branches:
      - main

jobs:
  format-code:
    if: github.event.pull_request.merged == true
    uses: testsnake/prettier-auto-format-action/.github/workflows/auto-format-yarn.yml@main # or auto-format-pnpm.yml, auto-format-npm.yml
    with:
      ref: ${{ github.event.pull_request.merge_commit_sha }}
      node-version: '16'
      runs-on: ubuntu-latest
      commit-message: 'chore: format code with prettier'
      file-pattern: 'src/**/*.*'
      fetch-depth: 0
    secrets: inherit
```