# 🚀 Release Action

Automated release workflow with conventional commits and changelog generation.

## ✨ Features

- 🏷️ **Automated Versioning** - Uses conventional commits to determine version bumps
- 📝 **Changelog Generation** - Beautiful changelogs with emojis
- 📦 **Generic Artifact Upload** - Upload any build artifacts to GitHub releases
- 🎨 **VS Code Marketplace** - Optional publishing to VS Code marketplace
- 🔄 **Multiple Package Managers** - Supports npm, pnpm, and yarn
- ⚡ **Flexible** - Customizable build and package commands

## 🎯 Quick Start

Create `.github/workflows/release.yml`:

```yaml
name: Release

on:
  workflow_dispatch:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Release
        uses: hsayed21/vscode-release-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          vsce-pat: ${{ secrets.VSCE_PAT }}
```

## 📋 Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `github-token` | GitHub token for releases | Yes | `${{ github.token }}` |
| `node-version` | Node.js version | No | `'20'` |
| `package-manager` | Package manager (npm/pnpm/yarn) | No | `'npm'` |
| `build-command` | Build command | No | `'npm run build'` |
| `skip-build` | Skip build step | No | `'false'` |
| `package-command` | Command to package artifacts | No | `''` |
| `upload-artifacts` | Glob pattern of files to upload | No | `''` |
| `publish-marketplace` | Publish to VS Code Marketplace | No | `'false'` |
| `vsce-pat` | VS Code Marketplace token | No | `''` |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `version` | Released version number |
| `tag` | Created git tag |
| `release-url` | GitHub release URL |

## 🔑 Setup Secrets

1. **GitHub Token** - Automatically available as `secrets.GITHUB_TOKEN`
2. **VS Code Marketplace Token** - Add `VSCE_PAT` to repository secrets:
   - Get token from [Visual Studio Marketplace](https://marketplace.visualstudio.com/manage)
   - Go to repository Settings → Secrets → New repository secret
   - Name: `VSCE_PAT`, Value: your token

## 📝 Conventional Commits

Use these commit prefixes for automatic versioning:

- `feat:` - New feature (minor version bump)
- `fix:` - Bug fix (patch version bump)
- `feat!:` or `BREAKING CHANGE:` - Breaking change (major version bump)
- `chore:`, `docs:`, `style:`, `refactor:` - No version bump

## 📚 Examples

### VS Code Extension - Upload VSIX to Release
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    package-command: npx vsce package
    upload-artifacts: '*.vsix'
```

### VS Code Extension - Publish to Marketplace
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    vsce-pat: ${{ secrets.VSCE_PAT }}
    publish-marketplace: true
```

### VS Code Extension - Both Upload & Publish
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    vsce-pat: ${{ secrets.VSCE_PAT }}
    package-command: npx vsce package
    upload-artifacts: '*.vsix'
    publish-marketplace: true
```

### Upload Multiple Artifacts
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    package-command: npm run package
    upload-artifacts: 'dist/*.zip dist/*.tar.gz *.vsix'
```

### Skip Build
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    skip-build: true
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

## 📄 License

MIT - see [LICENSE](LICENSE)
