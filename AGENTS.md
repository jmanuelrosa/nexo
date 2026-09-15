# Nexo contributor instructions

## Purpose

Nexo publishes reusable GitHub workflows and composite actions for personal repositories.
Keep shared automation explicit, narrowly scoped, and safe for both public and private callers.

## Repository layout

- `.github/workflows/`: reusable workflows invoked through `workflow_call`.
- `actions/<name>/action.yml`: composite actions for stable, repeated step sequences.
- `docs/decisions/`: accepted architecture decisions and compatibility rationale.
- `README.md`: public contracts, inputs, examples, and versioning guidance.
- `tasks/`: local planning artifacts and completed task checklists.

## Design boundaries

- Share independent policy jobs and repeated setup sequences only when multiple repositories use the same contract.
- Keep caller-specific triggers, path filters, concurrency, permissions, build commands, deployment environments, release logic, and secrets in caller repositories.
- Do not accept arbitrary commands as workflow or action inputs.
- Treat reusable workflow paths, action paths, inputs, outputs, secrets, defaults, permissions, and failure behavior as public APIs.
- Avoid breaking public APIs. If a breaking change is required, create a new major-version contract and document the migration.
- Consumers must be able to pin every Nexo reference to a full commit SHA.

## GitHub Actions conventions

- Reusable workflows expose only `workflow_call`; callers own event triggers.
- Declare the minimum required permissions explicitly.
- Set bounded job timeouts.
- Disable persisted checkout credentials unless a workflow specifically needs them.
- Pin third-party actions to full commit SHAs and retain a trailing version comment.
- `pnpm/action-setup@v4` is the documented exception until an audited SHA is adopted.
- Prefer typed workflow inputs and clear defaults.
- Composite action boolean-like inputs are strings, so compare them explicitly, for example `inputs.install == 'true'`.
- Give jobs and steps concise names that describe their observable purpose.

## Documentation

- Update `README.md` whenever a public contract or caller example changes.
- Record decisions that alter reuse boundaries, compatibility policy, or ownership in `docs/decisions/`.
- Do not edit an accepted decision to reverse it. Add a superseding decision instead.
- Examples must use `<nexo-sha>` rather than a branch or mutable tag for Nexo references.

## Validation

There is no project-level test runner.
For every change:

1. Parse all YAML files:

   ```sh
   ruby -e 'require "yaml"; Dir.glob("**/*.{yml,yaml}", File::FNM_DOTMATCH).each { |file| YAML.parse_file(file) }'
   ```

2. Review reusable workflow permissions, triggers, input types, failure behavior, and checkout settings.
3. Confirm every external action reference is an audited SHA or the documented `pnpm/action-setup@v4` exception.
4. Confirm `README.md` matches all changed paths, inputs, defaults, and examples.
5. Run `git diff --check`.
