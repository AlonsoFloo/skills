---
description: Verify pre-commit installation, run hooks across all files with minimal output, and auto-fix any issues
agent: flo-coder
allowed-tools: [Bash, Read, Grep]
---

# Run and Fix Pre-Commit Hooks

Execute this workflow with the `flo-coder` agent:

## 1. Check Installation

Verify that `pre-commit` is installed and available in the environment:

```bash
which pre-commit >/dev/null 2>&1 || command -v pre-commit >/dev/null 2>&1
```

If `pre-commit` is not found, notify the user immediately and halt.

## 2. Execute Pre-Commit (Minimizing Output)

Run pre-commit against all files. Minimize token and context usage by focusing only on failures:

```bash
pre-commit run --all-files
```

## 3. Auto-Fix and Resolve Issues

- **Formatter / Auto-fix changes:** If hooks automatically modified files (e.g., `end-of-file-fixer`, `trailing-whitespace`, `mdformat`), inspect modified files via `git status -s`.
- **Linter / Schema / Gitleaks errors:** If any hook fails requiring manual intervention:
  - Extract only the relevant error lines/files to preserve context.
  - Apply the necessary code and formatting fixes to resolve each error.
  - Re-run `pre-commit run --all-files` (or `pre-commit run <hook_id> --all-files`) until all checks pass with code 0.

## 4. Final Summary

Provide a concise summary:

- Status of all hooks.
- Files modified or fixed.
