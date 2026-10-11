# APM Reference Template

This reference provides templates for Microsoft Agent Package Manager (APM) configuration:

1. `apm.yml` at the repository root
1. GitHub Actions workflow for APM audit: `.github/workflows/pr-apm-audit.yml`

## apm.yml Template

Ensure to use the latest tag and commit hash

```yaml
name: {REPOSITORY NAME}
author: {YOUR_NAME_OR_ORG}
version: 1.0.0
description: {REPOSITORY DESCRIPTION}
license: {LICENSE_TYPE}

targets:
  - agent-skills
  - copilot
  - antigravity
  - opencode
  - codex

dependencies:
  apm: []

devDependencies:
  apm:
    - AlonsoFloo/skills#af6453911bc505b9f2cdc9967c108f74c626a6ce #v2.6.0
```

## APM Audit Workflow Template (`.github/workflows/pr-apm-audit.yml`)

```yaml
name: PR APM Audit
on:
  pull_request:
    paths:
      - 'apm.yml'
      - 'apm.lock.yaml'
      - '.apm/**'
      - '.github/**'
      - '.opencode/**'
      - '.claude/**'
      - '.cursor/**'

concurrency:
  group: pr-apm-audit-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  audit:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1

      - uses: microsoft/apm-action@d723bb64ed70c135bbaf87d126b721dd2dae0439 # v1.10.0
        with:
          setup-only: 'true'
          apm-version: 'latest'

      - run: apm audit --ci --no-fail-fast --no-cache
```

## Notes

- Replace placeholders in `{}` with appropriate values for your repository.
- The `apm.yml` file defines the project's APM configuration, including targets, dependencies, and devDependencies.
- The audit workflow runs on pull requests to check APM dependencies using the `microsoft/apm-action`.
- After adding these files, run `apm install` to install APM dependencies.
