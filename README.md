# BeforeProd GitHub Action

This GitHub Action automatically deploys your application to [BeforeProd](https://beforeprod.com) and updates your PR description with the deployment URL. It also includes automatic cleanup of deployments when PRs are closed.

## Features

- Automatic deployments to BeforeProd
- Automatic PR description updates with deployment URLs
- Automatic cleanup of deployments when PRs are closed
- Support for both Go and JavaScript applications
- Secure credential handling
- Single action for both deployment and cleanup
- Preview URL logging for direct branch deployments (no PR required)
- Robust error handling with automatic retries for PR updates

## Requirements

### Secrets

Add these repository secrets (Settings → Secrets and variables → Actions):

- `BP_USER` – BeforeProd username
- `BP_PASSWORD` – BeforeProd password

### Workflow permissions (required for PR updates)

The deploy step updates the PR body via the GitHub API (`pulls.update`). Composite actions cannot grant token permissions themselves — **your calling workflow must declare them**.

Add a top-level `permissions` block (sibling of `on:` / `jobs:`, **not** nested under `on:`):

```yaml
permissions:
  contents: read
  pull-requests: write
```

Without `pull-requests: write`, deployment can succeed while **Update PR description** fails with:

`403 Resource not accessible by integration` (`x-accepted-github-permissions: pull-requests=write`)

If your repository default is **Read repository contents and packages permissions** (Settings → Actions → General → Workflow permissions), the workflow-level `permissions:` block above is still the right fix, as long as workflows are allowed to elevate the `GITHUB_TOKEN` permissions for the job. Alternatively you can set the repo default to read and write; the explicit workflow block remains recommended (least privilege).

Cleanup only needs to **read** the PR body to find the preview URL. `contents: read` is enough when the repo default already includes pull-request read access; you can still set `pull-requests: read` explicitly if you prefer.

### Pull request merge conflicts

GitHub does not run `pull_request` workflows while the PR has merge conflicts (it cannot create the temporary merge commit). Resolve conflicts with the base branch before expecting deploy or cleanup workflows to run.

## Usage

### Deployment

```yaml
name: BeforeProd preview app action
on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: read
  pull-requests: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v7

      - name: Create a preview app on beforeprod.com
        uses: beforeprod-com/preview-deployment-action@main
        with:
          action: 'deploy'
          platform: 'JS'  # or 'GO' for Go applications
          build_folder: './build'  # path to your build artifacts
        env:
          BP_USER: ${{ secrets.BP_USER }}
          BP_PASSWORD: ${{ secrets.BP_PASSWORD }}
```

The action updates the PR description with the deployment URL. It is intended for `pull_request` events (`opened`, `synchronize`, `reopened`).

### Cleanup

Add a separate workflow (e.g. `.github/workflows/cleanup.yml`) that runs when the PR is closed:

```yaml
name: Cleanup PR Deployments
on:
  pull_request:
    types: [closed]

permissions:
  contents: read
  pull-requests: read

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v7

      - name: Cleanup deployment
        uses: beforeprod-com/preview-deployment-action@main
        with:
          action: 'cleanup'
        env:
          BP_USER: ${{ secrets.BP_USER }}
          BP_PASSWORD: ${{ secrets.BP_PASSWORD }}
```

## Action structure

**Unified action** (`action.yml`):

- Handles both deployment and cleanup via the `action` input (`deploy` or `cleanup`)
- Updates PR descriptions with deployment URLs on deploy
- Stops the BeforeProd app on cleanup (URL taken from the PR body)
- Ships the BeforeProd CLI binary

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `action` | The action to perform (`deploy` or `cleanup`) | Yes | `deploy` |
| `platform` | The platform your application runs on (`JS` or `GO`) | No | `JS` |
| `build_folder` | The folder containing your build artifacts | No | `./build` |

## Outputs

| Output | Description |
|--------|-------------|
| `url` | The URL where your application is deployed |
| `time` | The timestamp when the deployment was completed |

## Example

See the [example workflows](.github/workflows/) for complete examples.

## License

This software is proprietary and confidential. Unauthorized copying, distribution, or use is strictly prohibited. All rights reserved.
