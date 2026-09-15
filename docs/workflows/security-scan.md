# Security scan

[`security-scan.yml`](../../.github/workflows/security-scan.yml) is a reusable workflow that runs Bearer security scans with separate blocking and reporting severity levels.
Critical and high findings fail by default, while medium, low, and warning findings are reported without failing.

## Usage

Pin Nexo to a full commit SHA:

```yaml
name: Pull request

on:
  pull_request:

permissions:
  contents: read

jobs:
  security-scan:
    uses: jmanuelrosa/nexo/.github/workflows/security-scan.yml@<nexo-sha>
```

Narrow the policy without changing the workflow implementation:

```yaml
jobs:
  security-scan:
    uses: jmanuelrosa/nexo/.github/workflows/security-scan.yml@<nexo-sha>
    with:
      fail_severity: critical
      report_severity: high,medium
      report_findings: true
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `fail_severity` | `critical,high` | Comma-separated severities that fail the job |
| `report_severity` | `medium,low,warning` | Comma-separated severities reported without failing |
| `report_findings` | `true` | Whether to run the non-failing report pass |

## Behavior

- Grants read-only repository contents access.
- Checks out the repository without persisting credentials.
- Runs the blocking scan before the optional reporting scan.
- Times out after 10 minutes.
