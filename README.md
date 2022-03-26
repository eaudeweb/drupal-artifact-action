# Create a Drupal 8+ release


This GitHub action creates a Drupal 8+ release artifact uploaded to be reused for subsequent workflow steps.

Usage example:


```yml
on: [pull_request]
name: Tests and code
jobs:
  qa:
    runs-on: ubuntu-latest
    steps:

      - uses: cristiroma/drupal-release-action@alpha
        with:
          dev: true
          artifact: false
```

## Inputs:

- `dev` - (Default: `false`) - When `true` it installs all composer dependencies (including development). This is useful when creating a release that to run tests.
- `artifact` - (Default: `false`) - Since version `v1.1`. When `true` it creates an artifact which is uploaded to be reused in subsequent steps. Default filename is prefixed with `release-` and contains commit SHA 7 characters (e.g. `release-a0c2b31.tar.gz`).


## Outputs:

Since `v1.2`:

- `filename` - Artifact full archive filename, e.g. `release-a0c2b31.tar.gz`
- `base` - Artifact archive base name, e.g. `release-a0c2b31`
- `sha7` - Artifact 7-characters SHA , e.g. `a0c2b31`


Example 1. - Using output from a job to another job, where `build` creates the artifact archive and `deploy` downloads the artifact

```yml
on: push
jobs:
  build:
    name: "Create release archive"
    runs-on: ubuntu-latest
    outputs:
      filename: ${{ steps.release.outputs.filename }}
      base: ${{ steps.release.outputs.base }}
      sha: ${{ steps.release.outputs.sha }}
    steps:
      - name: "Build artifact"
        id: release
        uses: cristiroma/drupal-release-action@v1.2
        with:
          dev: false
          artifact: true

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

Example 2. - Using output from a step to next step

```yml
    steps:
      - name: "Build artifact"
        id: build
        uses: cristiroma/drupal-release-action@v1.2
        with:
          dev: false
          artifact: false

      - name: "Copy to server"
        run: |
          scp ${{ steps.build.outputs.filename }} server:${{ steps.build.outputs.filename }}

```
