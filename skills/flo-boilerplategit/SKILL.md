---
name: flo-boilerplategit
description: Boilerplate Git repository creation, auditing, cross-checking, and incremental configuration maintenance
license: MIT
metadata:
  author: github.com/AlonsoFloo
  keywords:
    - AlonsoFloo
    - flo
    - boilerplate
    - git
    - devops
    - repository-setup
---

# Boilerplate Git Repository Management

> Standardization, cross-checking, and automated setup of Git repositories

Treat the rules below as operational guidance for AI coding agents and DevOps engineers when initializing new Git repositories or cross-checking established ones against organizational standards. For reference configurations and templates, see [references/templates.md](references/templates.md).

## Main Goal

Establish and enforce a consistent, high-quality, secure DevOps baseline for all Git repositories, ensuring proper CI/CD workflows, automated dependency updates, release automation, code quality hooks, and standard documentation.

## Step Order & Workflow Rules

______________________________________________________________________

## Rule 1: Inspect Repository State and Context

**Description:** Always assess the repository's current status before making changes. Determine if the environment is a brand-new uninitialized folder or an existing Git repository with pre-existing code and configurations.

**Execution Steps:**

1. Check if `.git` directory exists (`git rev-parse --is-inside-work-tree`). If not, initialize Git repository (`git init`).
1. Identify project attributes: repository name, primary programming languages/frameworks, license, default branch (`main` or `master`), and owner/organization.
1. List existing files to avoid overwriting project-specific work.

______________________________________________________________________

## Rule 2: Cross-Check Against Standard Boilerplate Matrix

**Description:** Compare the repository against the 11 core DevOps boilerplate components. Report missing or outdated configurations before applying updates.

**Boilerplate Components Matrix:**

1. **README (`README.md`)**: Project overview, feature summary, quickstart, installation, usage, and badges.
1. **AGENTS (`AGENTS.md`)**: Guidance, index, and constraints for AI coding agents.
1. **Release Please (`release-please-config.json` & `.release-please-manifest.json`)**: Automated semantic releases and changelog generation.
1. **Renovate (`renovate.json`)**: Automated dependency update management.
1. **Pre-commit (`.pre-commit-config.yaml`)**: Git pre-commit hooks for linting, secret scanning, formatting, and commit validation.
1. **GitHub Workflows (`.github/workflows/`)**: Actions workflows for PR validation (`pr-checks.yml`) and releases (`release-please.yml`).
1. **GitHub Templates (`.github/ISSUE_TEMPLATE/` & `.github/PULL_REQUEST_TEMPLATE.md`)**: Standard issue and pull request templates.
1. **Gitignore (`.gitignore`)**: Environment, OS, IDE, and build artifact exclusion patterns.
1. **Gitconfig / Git Attributes (`.gitattributes` & `.gitconfig`)**: Line endings, diff drivers, and Git settings.
1. **Code Owners (`.github/CODEOWNERS`)**: Repository ownership and review requirements.
1. **EditorConfig (`.editorconfig`)**: Cross-editor indentation, character set, and trailing whitespace rules.

______________________________________________________________________

## Rule 3: Apply Smart Incremental Updates

**Description:** When operating on an already setupped repository, perform non-destructive updates. Merge missing keys, append missing sections, or create missing boilerplate files without overwriting customized project settings.

**❌ DON'T**

Overwriting an existing `README.md` or `.gitignore` without checking project-specific content.

**✅ DO**

Preserve existing project description in `README.md` while adding missing sections (e.g., pre-commit instructions or status badges). Append missing patterns to `.gitignore`.

______________________________________________________________________

## Rule 4: Load Reference Configurations

**Description:** Use standardized reference configurations defined in [references/templates.md](references/templates.md) when generating or updating boilerplate files. Ensure pinned versions, secure defaults, and valid syntax across JSON, YAML, and Markdown files.

______________________________________________________________________

## Rule 5: Validate Setup and Execute Pre-Commit Hooks

**Description:** After generating or updating boilerplate files, run validation tools to confirm syntactical correctness and verify that pre-commit hooks pass across all files.

**Execution Steps:**

1. Validate JSON and YAML file syntax.
1. Ensure pre-commit is installed and execute `pre-commit run --all-files`.
1. Auto-fix any formatting issues introduced during boilerplate generation.
