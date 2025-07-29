# BeforeProd GitHub Action

This GitHub Action automatically deploys your application to [BeforeProd](https://beforeprod.com) and updates your PR description with the deployment URL. It also includes automatic cleanup of deployments when PRs are closed.

## Features

- 🚀 Automatic deployments to BeforeProd
- 📝 Automatic PR description updates with deployment URLs
- 🔄 Automatic cleanup of deployments when PRs are closed
- 🛠️ Support for both Go and JavaScript applications
- 🔒 Secure credential handling
- 📦 Single action for both deployment and cleanup
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
        uses: beforeprod-com/preview-deployment-action@main
        with:
          action: 'deploy'
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
        uses: beforeprod-com/preview-deployment-action@main
        with:
          action: 'cleanup'
        env:
          BP_USER: ${{ secrets.BP_USER }}
          BP_PASSWORD: ${{ secrets.BP_PASSWORD }}
```

## Action Structure

The action is organized into a single unified component:

**Unified Action** (`action.yml`)
- Handles both deployment and cleanup based on the `action` input
- Updates PR descriptions with deployment URLs
- Automatically cleans up deployments when PRs are closed
- Contains the BeforeProd CLI binary

## Inputs

### Action Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `action` | The action to perform (`deploy` or `cleanup`) | Yes | `deploy` |
| `platform` | The platform your application runs on (`JS` or `GO`) | No | `JS` |
| `build_folder` | The folder containing your build artifacts | No | `./build` |

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
