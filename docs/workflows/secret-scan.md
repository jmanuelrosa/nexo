# Secret scan

[`secret-scan.yml`](../../.github/workflows/secret-scan.yml) is a reusable workflow that runs Gitleaks against the caller's complete repository history.
Detected leaks fail the workflow.

## Usage

Pin Nexo to a full commit SHA:

```yaml
name: Pull request

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  secret-scan:
    uses: jmanuelrosa/nexo/.github/workflows/secret-scan.yml@<nexo-sha>
    secrets:
      gitleaks_license: ${{ secrets.GITLEAKS_LICENSE }}
```

The `gitleaks_license` mapping is optional and can be removed when the caller has no license secret.

## Secrets

| Secret | Required | Description |
|---|---|---|
| `gitleaks_license` | No | Gitleaks license for organization features |

## Behavior

- Grants read-only repository contents access.
- Checks out the complete repository history without persisting credentials.
- Disables Gitleaks comments and artifact uploads.
- Times out after 10 minutes.
