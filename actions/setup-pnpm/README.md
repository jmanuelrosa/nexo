# Setup pnpm

[`setup-pnpm`](action.yml) is a composite action that sets up pnpm and Node, enables the pnpm cache, and installs locked dependencies.
pnpm reads its version from the caller's `packageManager` field in `package.json`.

## Usage

Check out the caller repository first and pin Nexo to a full commit SHA:

```yaml
steps:
  - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
    with:
      persist-credentials: false

  - uses: jmanuelrosa/nexo/actions/setup-pnpm@<nexo-sha>
```

For a package in a subdirectory:

```yaml
- uses: jmanuelrosa/nexo/actions/setup-pnpm@<nexo-sha>
  with:
    node-version-file: .nvmrc
    cache-dependency-path: pnpm-lock.yaml
    working-directory: apps/web
```

For an npm publication job, provide the registry URL:

```yaml
- uses: jmanuelrosa/nexo/actions/setup-pnpm@<nexo-sha>
  with:
    registry-url: https://registry.npmjs.org
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `node-version-file` | `.nvmrc` | Node version file relative to the repository root |
| `cache-dependency-path` | `pnpm-lock.yaml` | Dependency file used to key the pnpm cache |
| `working-directory` | `.` | Directory in which dependencies are installed |
| `registry-url` | empty | Optional npm registry URL written to the runner configuration |
| `install` | `true` | Whether to run `pnpm install --frozen-lockfile` |

Set `install: "false"` when the caller only needs the toolchain and cache.
