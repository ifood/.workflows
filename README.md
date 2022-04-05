# .workflows

Shared workflow for iFood open source projects.

## NPM Validate

Build and check code format. Requires and call `format:check` and `build` npm scripts.

- **Path:** `ifood/.workflows/.github/workflows/npm_validate.yml@main`
- **Parameters:** *none*
- **Secrets:** *none*

#### Example

```yml
name: Validate

on:
  workflow_call:
  push:
    branches:
      - 'main'
  pull_request:
    branches:
      - 'main'

jobs:
  validate_project:
    uses: ifood/.workflows/.github/workflows/npm_validate.yml@main
```

## NPM Publish

Build and publish to npm ifood organization. Requires `build` npm script, and is lerna and release-it compatible.

- **Path:** `ifood/.workflows/.github/workflows/npm_publish.yml@main`
- **Parameters:**
    - **bump_type**: Specify what semver path will be released (patch, minor, major). (default: minor, required: false)
- **Secrets:**
    - **NPM_DEPLOY_TOKEN**: [NPM automation Access token](https://docs.npmjs.com/creating-and-viewing-access-tokens) that will be used to deploy the project. (required: true)


#### Example:
```yml
name: Release

on:
  workflow_dispatch:
    inputs:
      bump_type:
        description: 'Bump type'     
        required: true
        default: 'minor' 
        type: choice
        options:
        - patch
        - minor
        - major

jobs:
  release:
    uses: ifood/.workflows/.github/workflows/npm_publish.yml@master
    secrets:
      NPM_DEPLOY_TOKEN: ${{ secrets.NPM_DEPLOY_TOKEN }}
    with:
      bump_type: ${{ github.event.inputs.bump_type }}
```

## Site Validate

Build and check code format of docusaurus website.

- **Path:** `ifood/.workflows/.github/workflows/site_validate.yml@main`
- **Parameters:**
    - **directory**: Specify the directory that contains the docusaurus documentation. (default: ./website)
- **Secrets:** *none*

#### Example

```yml
name: Validate

on:
  workflow_call:
  push:
    branches:
      - 'main'
  pull_request:
    branches:
      - 'main'

jobs:
  validate_project:
    # .. project related validations
  validate_site:
    uses: ifood/.workflows/.github/workflows/site_validate.yml@main
    with:
      directory: ./website
```

## Site Publish 

Build and deploy docusaurus site inside GitHub pages.

- **Path:** `ifood/.workflows/.github/workflows/site_publish.yml@main`
- **Parameters:**
    - **directory**: Specify the directory that contains the docusaurus documentation. (default: ./website)
- **Secrets:** *none*

#### Example

```yml
name: Deploy Site

on:
  workflow_dispatch:
  workflow_run:
    workflows: ['Release']
    branches: [main]
    types: 
      - completed

jobs:
  deploy-site:
    uses: ifood/.workflows/.github/workflows/site_publish.yml@main
    with:
      directory: ./documentation
```