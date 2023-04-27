# One of Labels Action

## Build

1. `npm i`
2. `npm i -g @vercel/ncc`
3. `ncc build index.js` - `dist/index.js` is regenerated.

## Example

Use a label to decide what version to publish.

```yaml
name: Release

on:
  push:
    branches:
      - main

jobs:
  release:
    runs-on: ubuntu-22.04

    steps:
      - uses: actions/checkout@v3

      - id: getPullRequest
        uses: MrDaar/pull-request-for-commit-action@master
        with:
          token: ${{ github.token }}
          sha: ${{ github.sha }}

      - id: semverLabel
        uses: MrDaar/one-of-labels-action@master
        with:
          fallback: 'patch'
          group: 'major,minor,patch'
          labels: '${{ join(fromJson(steps.getPullRequest.outputs.pullRequest).labels.*.name) }}'

      - id: computeTag
        uses: craig-day/compute-tag@v10
        with:
          github_token: ${{ github.token }}
          version_scheme: semantic
          version_type: ${{ steps.semverLabel.outputs.label }}
```
