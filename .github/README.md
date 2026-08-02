# Beforeprod.com Deployment GitHub Action

Canonical documentation lives in the [root README](../README.md).

## Permissions reminder

Updating the PR body requires workflow-level token permissions. In the **calling** workflow (not inside this action):

```yaml
permissions:
  contents: read
  pull-requests: write
```

`permissions` must be a top-level key next to `on:` / `jobs:`. See the [root README](../README.md#workflow-permissions-required-for-pr-updates) for secrets setup, cleanup permissions, and merge-conflict behavior.
