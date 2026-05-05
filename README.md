# Free Disk Space

A GitHub Action that frees up disk space on `ubuntu-latest` runners by removing preinstalled toolchains, large apt packages, Docker images, and swap. Useful when building large container images or working with multi-GB datasets that exceed the runner's default ~14 GB free space.

## Usage

```yaml
- uses: seventwo-studio/free-disk-space@v1
  with:
    android: true
    dotnet: true
    haskell: true
    large-packages: true
    docker-images: true
    tool-cache: false
    swap-storage: true
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
| `verbose`        | `true`  | —       | Prints `df -h /` before and after.                                                             |

## Versioning

Tags follow semver. Pin to a SHA in production:

```yaml
- uses: seventwo-studio/free-disk-space@<SHA> # v1.0.0
```

## License

MIT.
