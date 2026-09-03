# Pre-commit Reference Template (`.pre-commit-config.yaml`)

```yaml
fail_fast: true
default_install_hook_types: [pre-commit, commit-msg]
exclude: |
  (?x)^(
    CHANGELOG\.md$
    | \.agents/skills/
    | \.agents/apm-hooks.json
    | \.agents/hooks.json
    | \.agents/hooks/
    | \.agents/mcp_config.json
    | \.opencode/agents/
    | \.opencode/commands/
    | opencode.json
    | \.github/agents/
    | \.github/hooks/
    | \.github/prompts/
    | \.github/mcp.json
  )
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: check-json
      - id: check-xml
      - id: check-yaml
      - id: check-toml
      - id: check-added-large-files
      - id: requirements-txt-fixer
      - id: end-of-file-fixer
      - id: trailing-whitespace
      - id: check-executables-have-shebangs
      - id: check-shebang-scripts-are-executable
      - id: check-case-conflict
      - id: check-illegal-windows-names
      - id: check-symlinks
      - id: destroyed-symlinks
      - id: check-merge-conflict
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.30.1
    hooks:
      - id: gitleaks
  - repo: https://github.com/hukkin/mdformat
    rev: 1.0.0
    hooks:
      - id: mdformat
        additional_dependencies:
          - mdformat-gfm
          - mdformat-ruff
          - mdformat-frontmatter
          - ruff
  - repo: https://github.com/commitizen-tools/commitizen
    rev: v4.18.0
    hooks:
      - id: commitizen
        stages: [commit-msg]
  - repo: https://github.com/shellcheck-py/shellcheck-py
    rev: v0.11.0.1
    hooks:
      - id: shellcheck
        args: ["--severity=warning"]
```

## `.github/workflows/pr-checks.yml`

```yaml
name: PR Checks

on:
  pull_request:
    types: [opened, edited, synchronize]

permissions:
      contents: read
      pull-requests: write
      issues: write

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
      - uses: pre-commit/action@2c7b3805fd2a0fd8c1884dcaebf91fc102a13ecd # v3.0.1

  pr-title:
    runs-on: ubuntu-latest
    steps:
      - uses: amannn/action-semantic-pull-request@48f256284bd46cdaab1048c3721360e808335d50 # v6.1.1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```

## `.commitlintrc.js`

```javascript
module.exports = {
  extends: ['@commitlint/config-conventional'],
};
```

## `.github/workflows/pr-checks.yml`

```yaml
name: PR Checks

on:
  pull_request:
    types: [opened, edited, synchronize]

permissions:
      contents: read
      pull-requests: write
      issues: write

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
      - uses: pre-commit/action@2c7b3805fd2a0fd8c1884dcaebf91fc102a13ecd # v3.0.1

  pr-title:
    runs-on: ubuntu-latest
    steps:
      - uses: amannn/action-semantic-pull-request@48f256284bd46cdaab1048c3721360e808335d50 # v6.1.1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```
