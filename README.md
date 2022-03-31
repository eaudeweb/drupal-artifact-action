# Create a Drupal 8+ release

This GitHub action creates a Drupal 8+ release artifact uploaded to be reused for subsequent workflow steps.

## Usage


```yml
on: [pull_request]
name: Tests and code
jobs:
  qa:
    runs-on: ubuntu-latest
    steps:

      - uses: cristiroma/drupal-release-action@alpha
```

## Environment

- `GITHUB_SHA` - It uses this environment variable to compute an artifact filename based on first 7 characters from SHA.

## Outputs

Since `v1.2`:

- `filename` - Artifact full archive filename, e.g. `release-a0c2b31.tar.gz`
- `base` - Artifact archive base name, e.g. `release-a0c2b31`
- `sha7` - Artifact 7-characters SHA , e.g. `a0c2b31`

**Example 1** - Using output from a job to another job, where `build` creates the artifact archive and `deploy` downloads the artifact

```yml
on: push
jobs:
  build:
    name: "Create release archive"
    runs-on: ubuntu-latest
    outputs:
      filename: ${{ steps.release.outputs.filename }}
      base: ${{ steps.release.outputs.base }}
      sha7: ${{ steps.release.outputs.sha7 }}
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 1
      - uses: cristiroma/drupal-install-action@main
      - name: "Build artifact"
        id: release
        uses: cristiroma/drupal-release-action@main

  deploy:
    name: "Deploy release"
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: ${{ needs.build.outputs.filename }}
```

**Example 2** - Using output from a step to next step

```yml
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 1
      - uses: cristiroma/drupal-install-action@main
      - name: 'Build artifact'
        id: build
        uses: cristiroma/drupal-release-action@main
      - name: "Copy to server"
        run: |
          scp ${{ steps.build.outputs.filename }} server:${{ steps.build.outputs.filename }}
```
