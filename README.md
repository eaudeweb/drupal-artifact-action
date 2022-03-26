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
```
