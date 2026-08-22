---
description: Initialize or cross-check and update a Git repository with standard DevOps boilerplate configurations
agent: flo-boilerplategit
allowed-tools: [Bash, Read, Grep, Write, Edit]
---

# Git Repository Boilerplate Setup & Audit Workflow

Execute this workflow with the `flo-boilerplategit` agent and load the `flo-boilerplategit` skill:

## 1. Environment & Repository Discovery

1. Check if the current directory is a Git repository (`git rev-parse --is-inside-work-tree`). If not, run `git init`.
2. Detect repository metadata:
   - Directory/Repository name
   - Default branch name (`main` or `master`)
   - Primary language / stack / framework
   - Existing configuration files

## 2. Boilerplate Cross-Check Audit

Audit the repository against the 11 standard boilerplate components:

1. **README (`README.md`)**
2. **AGENTS (`AGENTS.md`)**
3. **Release Please (`release-please-config.json` & `.release-please-manifest.json`)**
4. **Renovate (`renovate.json`)**
5. **Pre-commit (`.pre-commit-config.yaml`)**
6. **GitHub Workflows (`.github/workflows/pr-checks.yml` & `.github/workflows/release-please.yml`)**
7. **GitHub Templates (`.github/ISSUE_TEMPLATE/` & `.github/PULL_REQUEST_TEMPLATE.md`)**
8. **Gitignore (`.gitignore`)**
9. **Gitconfig / Git Attributes (`.gitattributes` & `.gitconfig`)**
10. **Code Owners (`.github/CODEOWNERS`)**
11. **EditorConfig (`.editorconfig`)**

## 3. Incremental Boilerplate Generation & Merging

- **New Repositories**: Generate missing boilerplate configurations using canonical templates in `skills/flo-boilerplategit/references/templates.md`.
- **Established Repositories**: Cross-check existing configurations against the boilerplate standards. Incrementally merge missing rules, dependencies, workflow steps, or hooks without deleting or overwriting custom project logic.

## 4. Verification & Validation

1. Validate syntax across modified JSON, YAML, and configuration files.
2. Verify pre-commit installation and run hooks across all files:
   ```bash
   pre-commit run --all-files
   ```
3. Auto-fix formatting or hook failures if needed.

## 5. Summary Report

Provide a clear audit report summarizing:
- Repository state (New / Established).
- Components created or updated.
- Pre-commit verification status.
