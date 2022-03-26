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

Inputs:

- `dev` - (Default: `false`) - When `true` it installs all composer dependencies (including development). This is useful when creating a release that to run tests.
- `artifact` - (Default: `false`) - When `true` it creates an artifact which is uploaded to be reused in subsequent steps. Default filename is prefixed with `release-` and contains commit SHA 7 characters (e.g. `release-a0c2b31.tar.gz`).
