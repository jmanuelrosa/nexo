# Setup Bun

[`setup-bun`](action.yml) is a composite action that sets up Node and Bun, then installs locked dependencies with Bun.
It uses `.nvmrc` and the latest Bun release by default.

## Usage

Check out the caller repository first and pin Nexo to a full commit SHA:

```yaml
steps:
  - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
    with:
      persist-credentials: false

  - uses: jmanuelrosa/nexo/actions/setup-bun@<nexo-sha>
```

Use a Bun version file when the repository pins Bun:

```yaml
- uses: jmanuelrosa/nexo/actions/setup-bun@<nexo-sha>
  with:
    bun-version-file: .bun-version
```

For an application in a subdirectory:

```yaml
- uses: jmanuelrosa/nexo/actions/setup-bun@<nexo-sha>
  with:
    node-version-file: web/.nvmrc
    working-directory: web
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `node-version-file` | `.nvmrc` | Node version file relative to the repository root |
| `bun-version` | `latest` | Bun version used when no version file is provided |
| `bun-version-file` | empty | Optional Bun version file relative to the repository root |
| `working-directory` | `.` | Directory in which dependencies are installed |
| `install` | `true` | Whether to run `bun install --frozen-lockfile` |

`bun-version-file` takes precedence over `bun-version`.
Set `install: "false"` when the caller only needs the toolchain.
