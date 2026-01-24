# 🚀 VS Code Release Action

Automated release workflow for VS Code extensions with conventional commits and changelog generation.

## ✨ Features

- 🏷️ **Automated Versioning** - Uses conventional commits to determine version bumps
- 📝 **Changelog Generation** - Beautiful changelogs with emojis
- 📦 **VS Code Marketplace** - Automatic publishing to marketplace
- 🔄 **Multiple Package Managers** - Supports npm, pnpm, and yarn
- ⚡ **Flexible** - Customizable build commands and options

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
| `vsce-pat` | VS Code Marketplace token | No | `''` |
| `node-version` | Node.js version | No | `'20'` |
| `package-manager` | Package manager (npm/pnpm/yarn) | No | `'npm'` |
| `build-command` | Build command | No | `'npm run build'` |
| `publish-marketplace` | Publish to marketplace | No | `'true'` |
| `skip-build` | Skip build step | No | `'false'` |

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

### With pnpm
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    package-manager: pnpm
    github-token: ${{ secrets.GITHUB_TOKEN }}
    vsce-pat: ${{ secrets.VSCE_PAT }}
```

### Skip Build
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    skip-build: true
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Custom Build Command
```yaml
- uses: hsayed21/vscode-release-action@v1
  with:
    build-command: pnpm build:prod
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

## 📄 License

MIT - see [LICENSE](LICENSE)
