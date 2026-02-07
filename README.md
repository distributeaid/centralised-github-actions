# centralised-github-actions
Centralised Github Actions to be reused for Distribute Aid.

# Actions available

## PR Title Check
This GitHub Action enforces our naming convention for Pull Request titles, links the Kanban Issue with the PR and applies labels according to the PR type in the title.

### Inputs

| name           | description                                   | required | default |
|----------------|-----------------------------------------------|----------|---------|
| `github-token` | <p>GitHub token to use for authentication</p> | `true`   | N/A     |

### Examples

```yaml
name: PR Title Check
on:
  pull_request_target:
    types: [opened, edited, reopened, synchronize]

jobs:
  check-title:
    runs-on: ubuntu-latest
    steps:
      - name: Validate PR title and link to Issue
        uses: distributeaid/centralised-github-actions/.github/actions/pr-title-check-linking
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```
