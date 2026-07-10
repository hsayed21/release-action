# Release Action

Creates a versioned GitHub release with optional changelog generation and artifact uploads.

## Usage

### Basic

```yaml
name: Release

on:
  workflow_dispatch:
    inputs:
      version:
        description: Semantic version bump
        required: true
        default: patch
        type: choice
        options: [patch, minor, major]

permissions:
  contents: write

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: hsayed21/release-action@v1
        with:
          version: ${{ inputs.version }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Full example

```yaml
name: Release

on:
  workflow_dispatch:
    inputs:
      version:
        description: Semantic version bump
        required: true
        default: patch
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
          version: ${{ inputs.version }}
          initial-version: '0.0.0'
          release-command-shell: bash
          release-command: |
            ./scripts/build-release.sh "$RELEASE_VERSION"
          files-pattern: artifacts/*.zip
          generate-changelog: true
          include-non-conventional: false
          publish-vsce: false
          node-version: '24'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          VSCE_PAT: ${{ secrets.VSCE_PAT }}
```

## Release command variables

`release-command` can use these environment variables:

| Variable | Example |
|---|---|
| `RELEASE_VERSION` | `0.0.1` |
| `RELEASE_TAG` | `v0.0.1` |

## Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `version` | Semver bump: `patch`, `minor`, or `major`. | Yes | `patch` |
| `initial-version` | Initial version used by the action. | No | `0.0.0` |
| `release-command` | Command to run before publishing the release. | No | `''` |
| `release-command-shell` | Shell used for `release-command`. | No | `bash` |
| `files-pattern` | Artifact glob to upload. | No | `''` |
| `generate-changelog` | Generate and commit `CHANGELOG.md`. | No | `false` |
| `include-non-conventional` | Include non-conventional commits in release notes. | No | `false` |
| `publish-vsce` | Publish a VS Code extension. | No | `false` |
| `node-version` | Node.js version used by the action. | No | `24` |
| `release-version` | Exact version to release; overrides `version`. | No | `''` |

## Secrets

- `GITHUB_TOKEN` - Auto-available
- `VSCE_PAT` - For marketplace publishing (optional)

<details>
<summary><h2>Commit Hooks Setup (Optional)</h2></summary>

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

</details>

## License

MIT - see [LICENSE](LICENSE).
