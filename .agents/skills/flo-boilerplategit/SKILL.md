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

Treat the rules below as operational guidance for AI coding agents and DevOps engineers when initializing new Git repositories or cross-checking established ones against organizational standards.

## Main Goal

Establish and enforce a consistent, high-quality, secure DevOps baseline for all Git repositories, ensuring proper CI/CD workflows, automated dependency updates, release automation, code quality hooks, and standard documentation.
You are never allow to commit change automaticaly. Leave the final decision to the human developer.
Leave the changes in the staging area for review and approval before committing, unless explicitly authorized.

## Execution Order & Step Rules

______________________________________________________________________

## Rule 1: Inspect Repository State and Context

**Description:** Always assess the repository's current status before making changes. Determine if the environment is a brand-new uninitialized folder or an existing Git repository with pre-existing code and configurations.

**Execution Steps:**

1. Check if `.git` directory exists (`git rev-parse --is-inside-work-tree`). If not, initialize Git repository (`git init`).
1. Identify project attributes: repository name, primary programming languages/frameworks, license, default branch (`main` or `master`), and owner/organization.
1. List existing files to avoid overwriting project-specific work.

______________________________________________________________________

## Rule 2: Cross-Check Against Standard Boilerplate Matrix

**Description:** Audit the repository against all core DevOps boilerplate components. Report missing, incomplete, or outdated configurations before applying updates.

**Boilerplate Components Matrix:**

1. **README**: Overview, feature summary, quickstart, pre-commit instructions, and badges. See [references/readme.md](references/readme.md).
1. **AGENTS**: Guidance, index, and constraints for AI coding agents. See [references/agents.md](references/agents.md).
1. **Release Please (`release-please-config.json` & `.release-please-manifest.json`)**: Automated semantic releases and changelog generation. See [references/release-please.md](references/release-please.md).
1. **Renovate**: Automated dependency update management. See [references/renovate.md](references/renovate.md).
1. **Pre-commit**: Git pre-commit hooks for linting, secret scanning, formatting, and commit validation with commitlint. See [references/pre-commit.md](references/pre-commit.md).
1. **GitHub Templates (`.github/ISSUE_TEMPLATE/` & `.github/PULL_REQUEST_TEMPLATE.md`)**: Standard issue and pull request templates. See [references/github-templates.md](references/github-templates.md).
1. **Gitignore**: Environment, OS, IDE, and build artifact exclusion patterns. See [references/gitignore.md](references/gitignore.md).
1. **Code Owners**: Repository ownership and review requirements. See [references/codeowners.md](references/codeowners.md).
1. **EditorConfig**: Cross-editor indentation, character set, and trailing whitespace rules. See [references/editorconfig.md](references/editorconfig.md).
1. **License**: Repository open source license text. See [references/license.md](references/license.md).
1. **Security Policy**: Security guidelines and vulnerability disclosure instructions. See [references/security.md](references/security.md).

______________________________________________________________________

## Rule 3: Apply Smart Incremental Updates & Non-Destructive Merging

**Description:** When operating on an already setupped repository, perform non-destructive updates. Merge missing keys into JSON/YAML files, append missing sections or patterns to text/Markdown files, or create missing boilerplate files without overwriting customized project settings. If existing files or code are found, ensure the minimum baseline set defined across all reference templates is covered.

**❌ DON'T**

Overwriting an existing `README.md`, `renovate.json`, or `.gitignore` without checking project-specific content.

**✅ DO**

Preserve existing project description in `README.md` while adding missing sections (e.g., pre-commit instructions or status badges). Append missing patterns or definition. Ensure existing code/files cover the minimum baseline set of standard boilerplate elements defined in the reference templates, without overwriting customized project settings.

______________________________________________________________________

## Rule 4: Load Reference Configurations

**Description:** Use standardized reference configurations defined in individual reference files under `references/` when generating or updating boilerplate files. Ensure pinned versions, secure defaults, and valid syntax across JSON, YAML, and Markdown files.

- [references/readme.md](references/readme.md)
- [references/agents.md](references/agents.md)
- [references/release-please.md](references/release-please.md)
- [references/renovate.md](references/renovate.md)
- [references/pre-commit.md](references/pre-commit.md)
- [references/github-templates.md](references/github-templates.md)
- [references/gitignore.md](references/gitignore.md)
- [references/codeowners.md](references/codeowners.md)
- [references/editorconfig.md](references/editorconfig.md)
- [references/license.md](references/license.md)
- [references/security.md](references/security.md)

______________________________________________________________________

## Rule 5: Validate Setup and Generate Summary Report

**Description:** After generating or updating boilerplate files, run validation tools to confirm syntactical correctness, execute pre-commit hooks, and return a summary report.

**Execution Steps:**

1. Validate syntax across modified JSON, YAML, and configuration files.
1. Ensure pre-commit is installed and execute `pre-commit run --all-files`. Auto-fix any formatting issues introduced during boilerplate generation.
1. Output in the chat a summary audit report detailing:
   - Repository state (New or Established).
   - List of boilerplate components created or updated.
   - Final status of pre-commit checks and verification.
