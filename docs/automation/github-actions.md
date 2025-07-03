# GitHub Actions

The built-in CI/CD and automation tool for [GitHub](./version-control#github.md) repositories. They are configured using YAML files stored as `.github/workflows/<name>.yml`. GitHub does provide pre-written workflows for use cases like CI, CD, GitHub Pages etc.

> [GitHub Actions Documentation](https://docs.github.com/en/actions)  
> [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)  
> [Starter Workflows](https://github.com/actions/starter-workflows)

A typical GitHub Actions YAML file looks like:

```yaml
# .github/workflows/deploy.yaml

name: Workflow Name  # Optional workflow name

on:                  # Events that trigger the workflow
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight
  workflow_dispatch:      # Manual trigger via UI

permissions:         # GITHUB_TOKEN access control (least privilege)
  contents: write
  pull_requests: write

jobs:
  job_id:
    name: Job Name
    runs-on: ubuntu-latest
    needs: other_job_id        # Optional dependency
    if: ${{ condition }}       # Optional conditional execution
    environment: production    # Optional environment
    env:                       # Job-level environment variables
      NODE_ENV: production
    defaults:                  # Job-level defaults
      run:
        shell: bash
    steps:                     # Steps run in order on the same runner
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: Run a shell command
        run: echo "Hello World"

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Set env variable
        run: echo "MY_VAR=123" >> $GITHUB_ENV
```

**Table of Contents**:

- [Terminologies](#terminologies)
- [Events](#events)
- [Permissions](#permissions)
- [Expressions](#expressions)
  - [Contexts and Context Variables](#contexts-and-context-variables)
  - [Expression Functions](#expression-functions)
- [Secrets and Environments](#secrets-and-environments)
- [Advanced Processes](#advanced-processes)
  - [Matrix Strategy](#matrix-strategy)
  - [Reusable Workflows](#reusable-workflows)
  - [Conditional Execution](#conditional-execution)
  - [Caching](#caching)
  - [Artifacts](#artifacts)

## Terminologies

Every GitHub Actions workflow involves **6 core components**:

| Component    | Description  |
| --- | --- |
| **Workflow** | A YAML-defined automated process triggered by events like pushes, pull requests, or schedules. A repo can have multiple workflows. |
| **Event**    | A specific activity that triggers the workflow to run. Events can also be scheduled or manual. Defined via `on:`        |
| **Job**      | A sequence of steps executed on the same runner. Jobs are independent and defined using `jobs:` unless defined with `needs:`                   |
| **Steps** | A single action or script inside a job. They are executed sequentially on the same runner. Defined via `run:` (command) or `uses:` (reusable action). |
| **Runner**   | A server (VM) provided by GitHub or self-hosted that executes the job. Each job runs in a fresh VM.  Defined via `runs-on:` inside of a job.        |
| **Action**   | A reusable script or tool to perform common tasks (e.g., checkout repo, set up Node.js). Defined via `uses:` inside a job's steps.         |

## Events

Defined with the `on` keyword. These are triggered when:

- `push` - commits are pushed.
- `pull_request` - pull request is opened, synchronized, or reopened.
- `workflow_dispatch` - allows manual execution from the GitHub UI.
- `schedule` - scheduled times (cron syntax, UTC).
- `create`, `delete` - branches/tags are created or deleted.
- `issues`, `issue_comment` - an issue is created or comment is made.
- `release` - releases events.
- `registry_package` - packages events.
- `deployment`, `deployment_status` - a deployment is created or its status is updated.
- `check_run`, `check_suite` - CI checks start or complete.
- `status` - a commit status is updated (like from another CI service).

```yaml
on:
  push:
    branches: [main, dev]
    paths:
      - 'src/**'
      - '!docs/**'         # exclude changes in docs
  pull_request:
    branches: [main]
    types: [opened, synchronize]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target Environment'
        required: true
        default: 'staging'
  schedule:
    - cron: '0 0 * * 0'   # every Sunday at 00:00 UTC
  issues:
    types: [opened, closed]
  issue_comment:
    types: [created]
  release:
    types: [published]
  create:
    tags:
      - v*
  delete:
    branches:
      - feature/*
  registry_package:
    types: [published]
  deployment:
  deployment_status:
  check_suite:
    types: [completed]
  status:
```

## Permissions

Defined with the `permissions:` keyword.

GitHub Actions follows **least privilege** by default: all permissions are set to `none` unless explicitly granted.
You can define permissions at the **workflow level** or per **job**.

| Scope                   | Access                                   |
| ----------------------- | ---------------------------------------- |
| `none`, `read`, `write` | Define access level for each scope below |

Permissions are defined for a scope:

- `actions` — Manage workflows
- `attestations` — Create artifact attestations
- `checks` — Create/check CI checks
- `contents` — Read/write files/releases
- `deployments` — Manage deployments
- `discussions` — Read/write discussions
- `id-token` — For OIDC-based authentication
- `issues` — Read/write issue comments
- `models` — Use GitHub Copilot models
- `packages` — Manage GitHub Packages
- `pages` — GitHub Pages build
- `pull-requests` — PR comments and metadata
- `security-events` — Handle scanning results
- `statuses` — Commit status updates

## Expressions

GitHub Actions has something called **Reference Expressions**. They are defined like `${{ }}`. Inside them we can use **Context Variables** (using a **Context**).

```yml
# Use a secret
env:
  API_KEY: ${{ secrets.MY_SECRET }}

# Use matrix value
runs-on: ${{ matrix.os }}

# Conditional step
if: ${{ github.ref == 'refs/heads/main' }}

# Use output from a previous step
- name: Get result
  run: echo "Value: ${{ steps.fetch.outputs.result }}"

```

### Contexts and Context Variables

Using the `github` context, we can get info about:

- Repository `github.repository`, `github.repository_owner`
- Branches `github.ref`, `github.ref_name`, `github.ref_type`
- URL `github.server_url`, `github.api_url`, `github.graphql_url`
- Workflow itself `github.workflow`, `github.run_id`, `github.run_number`, `github.job`
- One who triggered the workflow `github.sha`, `github.actor`
- Event `github.event_name`, `github.event.head_commit.message`, `github.event.pull_request.title`, `github.event.issue.body`
- Source/Target branch `github.event.pull_request.head.ref`, `github.event.pull_request.base.ref`
- Branch protection `github.ref_protected`

We can get different types of variables via different contexts:

- Custom environment variables `env.<envvar_name>` — Defined at workflow, job, or step level
- Encrypted Secrets `secrets.<secret_name>` — Secret values (like API tokens)
- User-defined variables (defined in GitHub UI) with `vars.<variable_name>` — Custom version string or any other setting

Information about the

- Current job `job.status`
- All or specific job `jobs.<job_name>.result`, `jobs.<job_name>.outputs.artifact_url`
- Outputs/results of dependent jobs `needs.<job_name>.result`, `needs.<job_name>.outputs.artifact_url`
- Steps of current job `steps.<step_id>.outputs.output_name`, `steps.<step_id>.conclusion`, `steps.<step_id>.outcome`

We can also get info about the runner `runner`, group of them `matrix` or th strategy for them `strategy`.

- `runner.os`, `runner.arch`, `runner.name`, `runner.temp`
- `matrix.os`, `matrix.node`, `matrix.python`
- `strategy.fail-fast` — Whether to cancel all on one failure
- `strategy.max-parallel` — Max parallel jobs
- `strategy.matrix.os` — OS list

Lastly there is the `inputs` context for user provided inputs (via `workflow_dispatch`)

- `inputs.environment` — Environment input value
- `inputs.version` — Version input value

### Expression Functions

Used inside the `if` keyword.

| **Function**           | **Description**                                             | **Example**                                       |
| ---------------------- | ----------------------------------------------------------- | ------------------------------------------------- |
| `startsWith(a, b)`     | Returns true if string `a` starts with string `b`           | `${{ startsWith(github.ref, 'refs/tags/') }}`     |
| `endsWith(a, b)`       | Returns true if string `a` ends with string `b`             | `${{ endsWith(matrix.node, '18') }}`              |
| `contains(a, b)`       | Returns true if string `a` contains string `b`              | `${{ contains(github.actor, 'bot') }}`            |
| `format(fmt, args...)` | Returns a formatted string using placeholders like `{0}`    | `${{ format('release-{0}', github.run_number) }}` |
| `join(list, sep)`      | Joins items in a list using a separator                     | `${{ join(matrix.node, ', ') }}`                  |
| `toJSON(value)`        | Converts a value into its JSON string representation        | `${{ toJSON(matrix) }}`                           |
| `fromJSON(string)`     | Parses a JSON string back into an object or list            | `${{ fromJSON('["ubuntu", "windows"]')[0] }}`     |
| `hashFiles(globs...)`  | Returns a hash string of the contents of the matching files | `${{ hashFiles('**/package-lock.json') }}`        |
| `success()`            | Returns true if previous step/job succeeded                 | `${{ success() }}`                                |
| `failure()`            | Returns true if previous step/job failed                    | `${{ failure() }}`                                |
| `always()`             | Always returns true (even if prior steps fail)              | `${{ always() }}`                                 |
| `cancelled()`          | Returns true if job/run was cancelled                       | `${{ cancelled() }}`                              |

## Secrets and Environments

> Secrets store sensitive information like API keys, tokens, passwords) securely in encrypted form. They can be at different levels:

- Repository-level: Settings → Secrets and variables → Actions → Secrets
- Organization-level: Share across multiple repositories
- Environment-level: Scoped per environment

> Environments are named stages in your deployment process like dev, staging or production. They support environment-specific secrets, protection rules and custom environment URLs.

```yml
jobs:
  deploy:
    environment:
      name: production
      url: https://example.com
```

## Advanced Processes

### Matrix Strategy

Running jobs across multiple versions/configs in parallel. Useful for testing against multiple OSes, languages etc.

```yml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node: [16, 18]

runs-on: ${{ matrix.os }}

steps:
  - uses: actions/setup-node@v4
    with:
      node-version: ${{ matrix.node }}
```

### Reusable Workflows

Common workflow logic can be made into "modules" and called from other workflows.

```yml
jobs:
  call-common:
    uses: my-org/my-repo/.github/workflows/common.yml@main
    with:
      param1: "value"
    secrets:
      token: ${{ secrets.MY_SECRET }}
```

### Conditional Execution

Defined via the `if` keyword. Control whether a step or job should run.

```yml
# Run only on main branch
if: github.ref == 'refs/heads/main'

# Run only on PRs
if: github.event_name == 'pull_request'

# Run only when previous step fails
if: failure()

# Run if file changed
if: contains(github.event.head_commit.message, 'docs')
```

### Caching

Speeding up workflows by saving dependencies or build outputs between runs.

```yml
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

### Artifacts

Artifacts let you upload and persist files from a job for later use or inspection.

```yml
Upload

- uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: dist/

Download

- uses: actions/download-artifact@v4
  with:
    name: build-output
    path: ./dist
```
