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

## Commit Hooks Setup (Optional)

To enforce conventional commits locally (recommended for better commit history), run:

```bash
npm install --save-dev husky @commitlint/cli @commitlint/config-conventional
npx husky init
```

Create `commitlint.config.js`:

```javascript
module.exports = {
  extends: ['@commitlint/config-conventional'],
  helpUrl: 'https://www.conventionalcommits.org/',
  rules: {
    'body-leading-blank': [2, 'always'],
    'footer-leading-blank': [2, 'always'],
    'header-max-length': [2, 'always', 100],
    'type-enum': [2, 'always', [
      'feat', 'fix', 'docs', 'style', 'refactor', 'test', 'chore',
      'perf', 'ci', 'build', 'revert', 'wip', 'update', 'add', 'remove', 'rename', 'bump'
    ]],
    'type-case': [2, 'always', 'lower-case'],
  },
  parserPreset: {
    parserOpts: {
      headerPattern: /^(?:([\p{Emoji_Presentation}])\s+)?(\w+)(?:\((.*)\))?!?: (.*)$/u,
      headerCorrespondence: ['emoji', 'type', 'scope', 'subject'],
    },
  },
};
```

Create `.husky/commit-msg`:

```bash
npx --no -- commitlint --config commitlint.config.js --edit "$1" > /dev/null 2>&1 || {
  echo ""
  echo "   INVALID COMMIT MESSAGE"
  echo ""
  echo "   Format: <type>: <description>"
  echo "   Emoji: Optional prefix allowed"
  echo ""
  echo "   Examples:"
  echo "     fix: resolve login bug"
  echo "    🔥 feat: add dark mode"
  echo "     WIP: work in progress"
  echo ""
  echo "   Types: feat, fix, docs, style, refactor, test, chore,"
  echo "          perf, ci, build, revert, wip, update, add,"
  echo "          remove, rename, bump"
  exit 1
}
```

## License
MIT - see [LICENSE](LICENSE)
