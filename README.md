# nix-installer

Install Nix - works on GitHub-hosted runners, self-hosted runners, and inside containers.

## Features

- ✅ **Smart detection** - Skips install if Nix already present (container mode)
- ✅ **Flakes enabled** - `nix-command` and `flakes` enabled by default
- ✅ **Universal** - Works on ubuntu-latest, self-hosted, and nixos/nix containers
- ✅ **Fast** - No install needed when using containers

## Usage

### Basic

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: FlakeCache/nix-installer@v1
      - run: nix build
```

### With Container (Fastest on self-hosted)

```yaml
jobs:
  build:
    runs-on: self-hosted
    container: nixos/nix:latest
    steps:
      - uses: actions/checkout@v4
      - uses: FlakeCache/nix-installer@v1
      - run: nix build
```

### Universal Matrix (Same workflow for all runner types)

```yaml
jobs:
  build:
    strategy:
      matrix:
        include:
          - runner: self-hosted
            container: nixos/nix:latest
          - runner: ubuntu-latest
            container: nixos/nix:latest
          - runner: ubuntu-latest
            container: ''
    runs-on: ${{ matrix.runner }}
    container: ${{ matrix.container || null }}
    steps:
      - uses: actions/checkout@v4
      - uses: FlakeCache/nix-installer@v1
      - run: nix build
```

### With Extra Configuration

```yaml
- uses: FlakeCache/nix-installer@v1
  with:
    extra-conf: |
      substituters = https://cache.nixos.org https://nix-community.cachix.org
      trusted-public-keys = cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY= nix-community.cachix.org-1:mB9FSh9qf2dCimDSUo8Zy7bkq5CX+/rkCWyvRCYg3Fs=
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `extra-conf` | Extra lines to add to nix.conf | `''` |
| `nix-version` | Nix version to install (ignored in containers) | `latest` |

## Outputs

| Output | Description |
|--------|-------------|
| `nix-path` | Path to the Nix binary |
| `already-installed` | `true` if Nix was already installed (container mode) |

## How It Works

1. **Detects** if Nix is already installed (e.g., in `nixos/nix` container)
2. **Skips** installation if already present
3. **Installs** Nix if not found (using official installer)
4. **Configures** flakes and experimental features
5. **Adds** any extra configuration you provide

## License

MIT
