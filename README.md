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

Deploys an application to UpCloud infrastructure via Ansible, using dynamic inventory (UpCloud labels) and SOPS-encrypted secrets.

### Inputs

| name                    | description                                                                               | required |
|-------------------------|-------------------------------------------------------------------------------------------|----------|
| `environment`           | Deployment environment (`staging` or `prod`)                                              | `true`   |
| `branch`                | Branch to deploy                                                                          | `true`   |
| `app`                   | UpCloud app label value (e.g. `aggregated-public-information`)                            | `true`   |
| `repo_name`             | Directory name on server (e.g. `aggregated-public-information`)                           | `true`   |
| `repo_url`              | SSH clone URL (e.g. `git@github.com:distributeaid/aggregated-public-information.git`)     | `true`   |
| `infra_checkout_token`  | GitHub token with read access to `distributeaid/infrastructure`                           | `true`   |
| `upcloud_token`         | UpCloud API token                                                                         | `true`   |
| `ansible_ssh_private_key` | SSH private key for Ansible to connect to servers                                       | `true`   |
| `sops_age_key`          | Age private key for SOPS secret decryption                                                | `true`   |

### Secrets setup

`INFRA_CHECKOUT_TOKEN` must be a fine-grained PAT with **Contents: Read** on `distributeaid/infrastructure`, stored as an org secret accessible to caller repos.

### Example

```yaml
name: Deploy

on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Deployment environment"
        required: true
        type: choice
        options:
          - staging
          - prod
      branch:
        description: "Branch to deploy"
        required: true
        default: main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy application
        uses: distributeaid/centralised-github-actions/.github/actions/ansible-deploy@main
        with:
          environment: ${{ inputs.environment }}
          branch: ${{ inputs.branch }}
          app: aggregated-public-information
          repo_name: aggregated-public-information
          repo_url: git@github.com:distributeaid/aggregated-public-information.git
          infra_checkout_token: ${{ secrets.INFRA_CHECKOUT_TOKEN }}
          upcloud_token: ${{ secrets.UPCLOUD_TOKEN }}
          ansible_ssh_private_key: ${{ secrets.ANSIBLE_SSH_PRIVATE_KEY }}
          sops_age_key: ${{ secrets.SOPS_AGE_KEY }}
```
