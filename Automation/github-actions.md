# GitHub Actions
The built-in CI/CD tool for [GitHub](../Git/vcs-hosting.md#github) repositories. [Documentation](https://docs.github.com/en/actions)

It is configured using the `.github/workflows/<name>.yml` file. GitHub Actions provides a lot of pre-written workflows (CI, CD, Automation, Pages etc).

1. **Workflow** --> A configurable automated process that will run one or more jobs. 
    - Workflows are defined by a YAML file and will run when triggered by an event in your repository, or they can be triggered manually, or at a defined schedule. 
    - A repository can have multiple workflows and can reference a workflow within another workflow.
2. **Event** --> A specific activity in a repository that triggers a workflow run. 
    - An activity can originate from GitHub when someone creates a pull request, opens an issue, or pushes a commit to a repository. 
    - We can also trigger a workflow to run on a schedule, by posting to a REST API, or manually.
3. **Job** --> A set of steps in a workflow that is executed on the same runner. 
    - Each step is either a shell script that will be executed, or an action that will be run. 
    - Steps are executed in order and are dependent on each other. Since each step is executed on the same runner, you can share data from one step to another.
3. **Action** --> A custom application for the GitHub Actions platform that performs a complex but frequently repeated task. 
    - Use an action to help reduce the amount of repetitive code that you write in your workflow files. An action can pull your Git repository from GitHub, set up the correct toolchain for your build environment, or set up the authentication to your cloud provider.
4. **Runner** --> A server that runs your workflows when they're triggered. 
    - Each runner can run a single job at a time. GitHub provides Ubuntu Linux, Microsoft Windows, and macOS runners to run your workflows. Each workflow run executes in a fresh, newly-provisioned virtual machine.


```yaml
# .github/workflows/deploy.yaml

name: Workflow Name  # Optional

on:                  # Event(s) that trigger this workflow
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  schedule:
    - cron: '0 0 * * *'

permissions:
    contents: write
    pull_requests: write

jobs:                # One or more jobs
  job_id:            # Unique ID for this job
    name: Job Name   # Optional
    runs-on: ubuntu-latest  # OS or runner label
    needs: other_job_id     # Optional: run after another job
    if: ${{ condition }}    # Optional: conditional execution

    environment: production # Optional: sets environment

    env:            # Optional: set environment variables for this job
      NODE_ENV: production

    defaults:       # Optional: default values for steps
      run:
        shell: bash

    steps:          # Steps run sequentially within a job
      - name: Step name
        uses: actions/checkout@v4   # Reuse an existing action
      - name: Run a command
        run: echo "Hello World"
      - name: Use an action with inputs
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      - name: Set environment variable
        run: echo "MY_VAR=123" >> $GITHUB_ENV

```

## Workflow Keywords

1. `name` - The name of the workflow. GitHub displays the names of your workflows under your repository's "Actions" tab.
2. `env` - A map of variables that are available to the steps of all jobs in the workflow. You can also set variables that are only available to the steps of a single job or to a single step (`job.<id>.env`).
3. `defaults` - Use defaults to create a map of default settings that will apply to all jobs in the workflow. You can also set default settings that are only available to a job (`job.<id>.defaults`). It has 2 keywords: `shell` and `working_directory`.

### `On` Triggers


```yaml
on:
  push:
    branches: [main, dev]
  pull_request:
  schedule:
    - cron: '0 0 * * 0'   # weekly on Sunday
  workflow_dispatch:      # manual trigger
```

### Permissions
It controls what access the `GITHUB_TOKEN` has. We can define it globally or  just inside a job. It uses **least privilege** so any permission you don't specify defaults to `none`. Hence, there are 3 access levels: `none`, `read` or `write` (both read & write).
- actions — manage workflows
- attestations — create artifact attestations
- checks — create/check check runs and suites
- contents — read/write repository files and releases
- deployments — create/manage deployments
- discussions — read/write GitHub Discussions
- id-token — request OIDC token for secure auth
- issues — read/write issues and comments
- models — use GitHub AI Models API
- packages — upload/publish GitHub Packages
- pages — request GitHub Pages builds
- pull-requests — read/write pull requests
- security-events — handle code scanning and Dependabot alerts
- statuses — read/write commit statuses

