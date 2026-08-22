# Skills

A personal, agent-friendly repository of engineering rules, patterns, and
practices for software development.

[![Release](https://img.shields.io/github/v/release/AlonsoFloo/skills)](https://github.com/AlonsoFloo/skills/releases)
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![release-please](https://github.com/AlonsoFloo/skills/actions/workflows/release-please.yml/badge.svg)](https://github.com/AlonsoFloo/skills/actions)

## Purpose

Personal use for me, but you are welcome to view, and propose new stuffs.

## Getting Started

### Prerequisites

- APM (Agent Package Manager) installed

### Installation

<!-- x-release-please-start-version -->

```bash
apm install https://github.com/AlonsoFloo/skills.git#v2.1.0
```

<!-- x-release-please-end -->

### Contribution setup

When working on this repository a few setup are required.

```bash
pre-commit install
apm install
```

## 📂 Repository Structure

```text
.
├── agents/                         # Active working folder for agent definitions
│   └── [agent files..]
├── prompts/                        # Active working folder for prompt templates
│   └── [prompt files..]
├── skills/                         # Active working folder for skill modules
│   ├── [skills files..]
├── .apm/
│   └── [symlinks]                  # ⚠️ Simlinks based on root folders (Do not edit)
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
