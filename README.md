# nix-installer

Install Nix - works on GitHub-hosted runners, self-hosted runners, and inside containers.

**With FlakeCache binary cache enabled by default for faster builds!**

## Features

- ✅ **Smart detection** - Skips install if Nix already present (container mode)
- ✅ **Flakes enabled** - `nix-command` and `flakes` enabled by default
- ✅ **Universal** - Works on ubuntu-latest, self-hosted, and nixos/nix containers
- ✅ **Fast** - No install needed when using containers
- 🚀 **FlakeCache** - Binary cache enabled by default for faster builds

## Usage

### Basic (with FlakeCache)

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

### Disable FlakeCache

```yaml
- uses: FlakeCache/nix-installer@v1
  with:
    flakecache: 'false'
```

### Add Extra Binary Caches

```yaml
- uses: FlakeCache/nix-installer@v1
  with:
    extra-substituters: 'https://nix-community.cachix.org'
    extra-trusted-keys: 'nix-community.cachix.org-1:mB9FSh9qf2dCimDSUo8Zy7bkq5CX+/rkCWyvRCYg3Fs='
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

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `flakecache` | Enable FlakeCache binary cache | `true` |
| `extra-substituters` | Additional binary cache URLs (space-separated) | `''` |
| `extra-trusted-keys` | Additional trusted public keys (space-separated) | `''` |
| `extra-conf` | Extra lines to add to nix.conf | `''` |

## Outputs

| Output | Description |
|--------|-------------|
| `nix-path` | Path to the Nix binary |
| `already-installed` | `true` if Nix was already installed (container mode) |

## Binary Caches

By default, this action configures:

| Cache | URL | Priority |
|-------|-----|----------|
| **FlakeCache** | `https://c.flakecache.com` | 1st (fastest) |
| **NixOS** | `https://cache.nixos.org` | 2nd (fallback) |

## How It Works

1. **Detects** if Nix is already installed (e.g., in `nixos/nix` container)
2. **Skips** installation if already present
3. **Installs** Nix if not found (using official installer)
4. **Configures** flakes, FlakeCache, and experimental features
5. **Adds** any extra caches/configuration you provide

## License

MIT
