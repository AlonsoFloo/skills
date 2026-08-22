# GitHub Workflows Reference Template (`.github/workflows/`)

## `.github/workflows/pr-checks.yml`

```yaml
name: PR Checks

on:
  pull_request:
    branches: [main, master]

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install pre-commit
      - run: pre-commit run --all-files
```

## `.github/workflows/release-please.yml`

```yaml
name: Release Please

on:
  push:
    branches: [main, master]

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
```
