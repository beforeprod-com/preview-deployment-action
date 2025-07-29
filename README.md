# BeforeProd GitHub Action

This GitHub Action automatically deploys your application to [BeforeProd](https://beforeprod.com) and updates your PR description with the deployment URL. It also includes automatic cleanup of deployments when PRs are closed.

## Features

- 🚀 Automatic deployments to BeforeProd
- 📝 Automatic PR description updates with deployment URLs
- 🔄 Automatic cleanup of deployments when PRs are closed
- 🛠️ Support for both Go and JavaScript applications
- 🔒 Secure credential handling
- 📦 Independent binary management for each action (currently because of KISS)
- 📊 Preview URL logging for direct branch deployments (no PR required)
- 🔄 Robust error handling with automatic retries for PR updates

## Usage

### Deployment Action

You can use this action directly from this repository:

```yaml
name: BeforeProd preview app action
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Create a preview app on beforeprod.com
        uses: beforeprod-com/preview-deployment-action-tmp@main
        with:
          platform: 'JS'  # or 'GO' for Go applications
          build_folder: './build'  # path to your build artifacts
        env:
          BP_USER: ${{ secrets.BP_USER }}
          BP_PASSWORD: ${{ secrets.BP_PASSWORD }}
```

> **Note**: The action will automatically update PR descriptions with deployment URLs. The action only runs on pull request events (opened, updated, or reopened) to ensure deployments are only created when needed.

### Cleanup Action

The cleanup action automatically removes deployments when PRs are closed. Add this to a separate workflow file (e.g., `.github/workflows/cleanup.yml`):

```yaml
name: Cleanup PR Deployments
on:
  pull_request:
    types: [closed]

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Cleanup deployment
        uses: beforeprod-com/preview-deployment-action-tmp/cleanup-action.yml@main
        env:
          BP_USER: ${{ secrets.BP_USER }}
          BP_PASSWORD: ${{ secrets.BP_PASSWORD }}
```

## Action Structure

The action is organized into two main components:

1. **Deployment Action** (`action.yml`)
   - Handles the deployment of your application
   - Updates PR descriptions with deployment URLs
   - Triggered on `pull_request` events (opened, synchronize, reopened)
   - Contains the BeforeProd CLI binary

2. **Cleanup Action** (`cleanup-action.yml`)
   - Automatically cleans up deployments when PRs are closed
   - Removes the deployment from BeforeProd
   - Triggered on `pull_request` events with type `closed`
   - Contains the BeforeProd CLI binary

## Inputs

### Deployment Action Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `platform` | The platform your application runs on (`JS` or `GO`) | Yes | `JS` |
| `build_folder` | The folder containing your build artifacts | Yes | `./build` |

### Cleanup Action Inputs

The cleanup action doesn't require any inputs as it automatically extracts the necessary information from the PR description.

## Outputs

### Deployment Action Outputs

| Output | Description |
|--------|-------------|
| `url` | The URL where your application is deployed |
| `time` | The timestamp when the deployment was completed |

## Example

See the [example workflows](.github/workflows/) for complete examples of how to use both actions.

## License

This software is proprietary and confidential. Unauthorized copying, distribution, or use is strictly prohibited. All rights reserved.
