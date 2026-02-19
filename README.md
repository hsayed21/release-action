# Release Action

Automated releases with version bumping, changelog generation, and automatic rollback on failure.

## Usage

Create `.github/workflows/release.yml`:

```yaml
name: Release
on:
  workflow_dispatch:
    inputs:
      version:
        type: choice
        options: [patch, minor, major]

permissions:
  contents: write
  pull-requests: write

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: hsayed21/release-action@v1
        with:
          version: ${{ github.event.inputs.version }}
          # include-non-conventional: false
          # generate-changelog: false
          # files-pattern: 'dist/*.zip'
          # publish-vsce: false
          # node-version: '24'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          VSCE_PAT: ${{ secrets.VSCE_PAT }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version` | Semver bump (patch, minor, major) | Yes | `patch` |
| `publish-vsce` | Publish to VS Code Marketplace | No | `false` |
| `files-pattern` | Extra files to upload | No | `''` |
| `include-non-conventional` | Include non-conventional commits | No | `false` |
| `generate-changelog` | Generate CHANGELOG.md file | No | `false` |
| `node-version` | Node.js version for VSCE publish | No | `24` |

## Secrets

- `GITHUB_TOKEN` - Auto-available
- `VSCE_PAT` - For marketplace publishing (optional)

## License
MIT - see [LICENSE](LICENSE)