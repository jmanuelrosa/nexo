# Nexo

Reusable GitHub workflows and composite actions for secure, consistent CI automation.
Nexo means link in Spanish: it connects repositories to one maintained automation layer without hiding project-specific behavior.

## Reusable workflows

- [Secret scan](docs/workflows/secret-scan.md) runs Gitleaks against the complete repository history.
- [Security scan](docs/workflows/security-scan.md) runs Bearer with configurable blocking and reporting severity levels.

## Composite actions

- [Setup pnpm](actions/setup-pnpm/README.md) sets up pnpm and Node, restores the pnpm cache, and optionally installs locked dependencies.
- [Setup Bun](actions/setup-bun/README.md) sets up Bun and Node, then optionally installs locked dependencies.

## Using Nexo

Pin every Nexo reference to a full commit SHA.
This prevents an unrelated central change from altering a caller until its reference is deliberately updated.
The documentation uses `<nexo-sha>` as a placeholder for that immutable revision.

Caller repositories keep their own event triggers, path filters, concurrency, permissions, build commands, deployment environments, and release logic.

## Versioning

Workflow paths and action inputs are public APIs.
Breaking changes require a new major release.
Consumers should pin full commit SHAs and use GitHub Actions Dependabot updates to adopt releases through reviewable pull requests.
Nexo pins established third-party revisions by SHA; `pnpm/action-setup` currently follows its v6 tag because the source projects did not contain an audited commit pin for it.

## Boundaries

Nexo does not accept build, deploy, or release commands as string inputs.
Those commands express project behavior and remain visible in each repository.
A new shared building block belongs here only after multiple projects use the same contract, permissions, and failure policy.
