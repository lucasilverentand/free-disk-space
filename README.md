# Free Disk Space

A GitHub Action that reclaims disk space on `ubuntu-latest` runners by removing preinstalled toolchains, large apt packages, Docker images, and swap.

GitHub-hosted Ubuntu runners ship with ~14 GB free out of a 75 GB disk. Most of the rest is preinstalled compilers and SDKs you almost certainly don't need. Pruning them up front reclaims **30–50 GB**, which is the difference between "build succeeds" and "no space left on device" when you're producing container images, multi-arch artifacts, or working with sizable datasets.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## Quick start

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: lucasilverentand/free-disk-space@v1
      - uses: actions/checkout@v4
      # ...your build steps
```

The defaults are tuned to be safe for almost any workflow: it does **not** touch the runner tool cache (so `setup-node`, `setup-python`, `setup-go` etc. keep working), but it removes everything else that's commonly unused.

## Full example

```yaml
- uses: lucasilverentand/free-disk-space@v1
  with:
    android: true
    dotnet: true
    haskell: true
    large-packages: true
    docker-images: true
    tool-cache: false  # leave the hosted tool cache alone
    swap-storage: true
    verbose: true
```

## Inputs

| Input            | Default | Frees   | Notes                                                                                          |
| ---------------- | ------- | ------- | ---------------------------------------------------------------------------------------------- |
| `android`        | `true`  | ~9 GB   | Removes the Android SDK and NDK.                                                               |
| `dotnet`         | `true`  | ~1.6 GB | Removes the .NET runtime and SDKs.                                                             |
| `haskell`        | `true`  | ~5.2 GB | Removes GHC and Haskell tooling.                                                               |
| `large-packages` | `true`  | ~4 GB   | Purges Azure CLI, Google Cloud SDK, PowerShell, Firefox, Chromium, MongoDB, MySQL, MS SQL.     |
| `docker-images`  | `true`  | ~3 GB   | Runs `docker image prune --all`.                                                               |
| `tool-cache`     | `false` | ~6 GB   | Removes `$AGENT_TOOLSDIRECTORY`. Breaks `setup-node`, `setup-python`, etc. — opt in carefully. |
| `swap-storage`   | `true`  | ~4 GB   | Disables and removes the runner swap file.                                                     |
| `verbose`        | `true`  | —       | Prints `df -h /` before and after each step.                                                   |

All inputs accept `'true'` or `'false'` (strings, since GitHub Actions inputs are stringly-typed).

## Typical results

On a fresh `ubuntu-latest` runner with default inputs:

```
Before:  Avail  ~14 GB
After:   Avail  ~45 GB
Time:    ~45 s
```

Enabling `tool-cache: true` reclaims another ~6 GB but breaks `setup-*` actions that read from the cache — only do this if you bring your own toolchain.

## Common patterns

**Run early.** Put this step before `actions/checkout` so the cleanup happens on a fresh filesystem and doesn't interfere with anything you've already set up.

**Pair with Docker prune mid-job.** If your workflow builds many images sequentially, add an explicit `docker image prune -af` between builds — the initial prune doesn't help with images you create later.

**Keep `tool-cache: false` if you use `setup-node`/`setup-python`/`setup-go`.** Those actions resolve toolchains from `$AGENT_TOOLSDIRECTORY` and will fall back to a slow download if you remove it.

## Versioning

This action follows semver. The major version (`v1`) is a moving tag that tracks the latest compatible release.

For reproducible builds, pin to a commit SHA:

```yaml
- uses: lucasilverentand/free-disk-space@<SHA>  # v1.0.0
```

For convenience in non-critical workflows, a major-version tag is fine:

```yaml
- uses: lucasilverentand/free-disk-space@v1
```

## Contributing

Issues and PRs welcome. The action is a single composite workflow in `action.yml` — there's no build step. Test changes by referencing your fork directly:

```yaml
- uses: your-fork/free-disk-space@your-branch
```

## License

MIT — see [LICENSE](LICENSE).
