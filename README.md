# centralised-github-actions
Centralised Github Actions to be reused for Distribute Aid.

# Actions available

## PR Title Check
This GitHub Action enforces our naming convention for Pull Request titles, links the Kanban Issue with the PR. It expects issue type/issue number/ description. If we want to bypass it for a PR, we just need to have NOISSUE in the PR title.

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

## Ansible Deploy

This GitHub Action deploys an application to a server by checking out the specified branch, running `yarn install` and `yarn build`, and restarting the PM2 service. It connects to the server over SSH using the provided private key.

### Inputs

| name              | description                                                                              | required | default |
|-------------------|------------------------------------------------------------------------------------------|----------|---------|
| `host`            | <p>Server IP to deploy to</p>                                                            | `true`   | N/A     |
| `branch`          | <p>Branch to check out and deploy</p>                                                    | `true`   | N/A     |
| `repo_name`       | <p>Repository name — matches the cloned folder name and PM2 service name</p>            | `true`   | N/A     |
| `SSH_PRIVATE_KEY` | <p>Deploy SSH private key</p>                                                            | `true`   | N/A     |

### Examples

```yaml
name: Deploy

on:
  workflow_dispatch:
    inputs:
      host:
        required: true
        description: "Server IP"
      branch:
        required: true
        description: "Branch to deploy"

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy application
        uses: distributeaid/centralised-github-actions/.github/actions/ansible-deploy@main
        with:
          host: ${{ inputs.host }}
          branch: ${{ inputs.branch }}
          repo_name: aggregated-public-information
          SSH_PRIVATE_KEY: ${{ secrets.DEPLOY_SSH_PRIVATE_KEY }}
```
