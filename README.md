# Skills

A personal, agent-friendly repository of engineering rules, patterns, and
practices for software development.

[![Release](https://img.shields.io/github/v/release/AlonsoFloo/skills)](https://github.com/AlonsoFloo/skills/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Release Please](https://github.com/AlonsoFloo/skills/actions/workflows/release-please.yml/badge.svg)](https://github.com/AlonsoFloo/skills/actions)
[![APM Audit](https://github.com/AlonsoFloo/skills/actions/workflows/apm-audit-main.yml/badge.svg)](https://github.com/AlonsoFloo/skills/actions)

## Purpose

Personal use for me, but you are welcome to view, and propose new stuffs.
This repository is a APM package to provide easy skills, agents, prompts, etc...
See: [https://microsoft.github.io/apm/reference/package-types/](https://microsoft.github.io/apm/reference/package-types/)

All sources are in the folder `.apm/..`

## Getting Started

### Prerequisites

- APM (Agent Package Manager) installed

### Installation

To install this tool on your machine or repo, run:

#### A. All deps

<!-- x-release-please-start-version -->

```bash
apm install AlonsoFloo/skills.git#v2.4.1
```

<!-- x-release-please-end -->

#### B. Isolated SKILL

<!-- x-release-please-start-version -->

```bash
apm install AlonsoFloo/skills/.apm/skills/flo-software-craftsmanship#v2.4.1
```

<!-- x-release-please-end -->

### Contribution setup (CONTRIBUTOR ONLY)

When working on this repository a few setup are required, you need to run:

```bash
pre-commit install
apm install
```

## 📂 Repository Structure

```text
.
├── .apm/
│   ├── agents/                         # Active working folder for agent definitions
│   │   └── [agent files..]
│   ├── prompts/                        # Active working folder for prompt templates
│   │   └── [prompt files..]
│   ├── skills/                         # Active working folder for skill modules
│   │   └── [skills files..]
├── .agents/
│   └── [auto-generated]            # ⚠️ Managed by Microsoft APM (Do not edit)
└── .github/
    └── workflows
    └── ISSUE_TEMPLATE
    └── agents [auto-generated]     # ⚠️ Managed by Microsoft APM (Do not edit)
    └── prompts [auto-generated]    # ⚠️ Managed by Microsoft APM (Do not edit)
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

[MIT](LICENSE)

## References

- APM documentation : [https://microsoft.github.io/apm/](https://microsoft.github.io/apm/)
- Agents.md documentation : [https://agents.md/](https://agents.md/)
- DotAgentProtocol for `.agents` documentation : [https://dotagentsprotocol.com/](https://dotagentsprotocol.com/)
