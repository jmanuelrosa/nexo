# Nexo

Reusable GitHub workflows and composite actions for personal projects.
Nexo means link in Spanish: it connects repositories to one maintained automation layer without hiding project-specific behavior.

## Using Nexo

Pin every reference to a full Nexo commit SHA.
This prevents an unrelated central change from altering a caller until its reference is deliberately updated.
The examples use `<nexo-sha>` as a placeholder for that immutable revision.

Caller repositories keep their own event triggers, path filters, concurrency, build commands, deployment environments, and release logic.

## Reusable workflows

### [Secret scan](docs/workflows/secret-scan.md)

Runs Gitleaks against the complete repository history.
Comments and artifact uploads are disabled, checkout credentials are not persisted, and the token has read-only repository access.

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

### [Security scan](docs/workflows/security-scan.md)

Runs Bearer twice.
Critical and high findings fail by default, while medium, low, and warning findings are reported without failing.

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

The policy can be narrowed without changing the workflow implementation:

```yaml
jobs:
  security-scan:
    uses: jmanuelrosa/nexo/.github/workflows/security-scan.yml@<nexo-sha>
    with:
      fail_severity: critical
      report_severity: high,medium
      report_findings: true
```

| Input | Default | Description |
|---|---|---|
| `fail_severity` | `critical,high` | Comma-separated severities that fail the job |
| `report_severity` | `medium,low,warning` | Comma-separated severities reported without failing |
| `report_findings` | `true` | Whether to run the non-failing report pass |

## Composite actions

Composite actions run inside a caller job.
Check out the caller repository before invoking either setup action.

### [Setup pnpm](actions/setup-pnpm/README.md)

Sets up pnpm from the caller's `packageManager`, sets up Node from `.nvmrc`, restores the pnpm cache, and runs a frozen install.

```yaml
steps:
  - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
    with:
      persist-credentials: false

  - uses: jmanuelrosa/nexo/actions/setup-pnpm@<nexo-sha>
```

For an npm publication job:

```yaml
- uses: jmanuelrosa/nexo/actions/setup-pnpm@<nexo-sha>
  with:
    registry-url: https://registry.npmjs.org
```

| Input | Default | Description |
|---|---|---|
| `node-version-file` | `.nvmrc` | Node version file relative to the repository root |
| `cache-dependency-path` | `pnpm-lock.yaml` | File used to key the pnpm cache |
| `working-directory` | `.` | Directory in which dependencies are installed |
| `registry-url` | empty | Optional npm registry URL |
| `install` | `true` | Whether to install from the lockfile |

### [Setup Bun](actions/setup-bun/README.md)

Sets up Node from `.nvmrc`, sets up Bun, and runs a frozen install.
Bun defaults to `latest`, or a caller can provide a version file.

```yaml
steps:
  - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
    with:
      persist-credentials: false

  - uses: jmanuelrosa/nexo/actions/setup-bun@<nexo-sha>
    with:
      bun-version-file: .bun-version
```

For a project whose JavaScript application is under `web`:

```yaml
- uses: jmanuelrosa/nexo/actions/setup-bun@<nexo-sha>
  with:
    node-version-file: web/.nvmrc
    working-directory: web
```

| Input | Default | Description |
|---|---|---|
| `node-version-file` | `.nvmrc` | Node version file relative to the repository root |
| `bun-version` | `latest` | Bun version used without a version file |
| `bun-version-file` | empty | Optional Bun version file relative to the repository root |
| `working-directory` | `.` | Directory in which dependencies are installed |
| `install` | `true` | Whether to install from the lockfile |

## Versioning

Workflow paths and action inputs are public APIs.
Breaking changes require a new major release.
Consumers should pin full commit SHAs and use GitHub Actions Dependabot updates to adopt releases through reviewable pull requests.
Nexo pins established third-party revisions by SHA; `pnpm/action-setup` currently follows its v4 tag because the source projects did not contain an audited commit pin for it.

## Boundaries

Nexo does not accept build, deploy, or release commands as string inputs.
Those commands express project behavior and remain visible in each repository.
A new shared building block belongs here only after multiple projects use the same contract, permissions, and failure policy.

See [`docs/decisions/001-reuse-boundaries.md`](docs/decisions/001-reuse-boundaries.md) for the rationale.

## Automation audit

The [September 2026 personal-project audit](docs/audits/personal-automation-2026-09-15.md) maps duplicated automation to the current Nexo contracts and records why the remaining workflows stay local.
Caller migrations are deferred until Nexo is public and can be referenced by an immutable commit SHA.
