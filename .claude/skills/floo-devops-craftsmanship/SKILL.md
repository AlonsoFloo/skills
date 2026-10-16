---
name: floo-devops-craftsmanship
description: DevOps, AI-assisted development conventions & supply-chain security
license: Complete terms in LICENSE
metadata:
  author: github.com/AlonsoFloo
  keywords:
    - AlonsoFloo
    - floo
    - devops
    - ci-cd
    - supply-chain-security
    - git-commit
    - dependencies
---

# DevOps Craftsmanship

> DevOps, AI-assisted development conventions & supply-chain security

Treat the rules below as engineering guidance for AI coding assistants, code
reviewers, and software engineers. Each rule links to its own
reference file in [references/](references/) with extended examples and deep
dives.

## Commit & Push Policy

- **Local developer machine:** never commit or push automatically. Leave changes in the staging area so the developer can review them and decide, unless they explicitly ask you to commit or push.
- **Cloud agent session** (ephemeral sandbox such as Claude Code on the web, Codex cloud or Copilot coding agent): committing and pushing is allowed, because uncommitted work is lost when the session ends. Push only to a dedicated feature branch, never to the default branch, and never merge without human approval.

## Rule 1: Enforce Detailed Commit Messages for AI-Assisted Code

**Description:** When utilizing AI coding assistants, git commit messages must
be thorough and detailed. Document intent, architectural reasoning, modified
files, human review steps, and tests performed to ensure auditability. Prefix
commit messages with `AI.`.

**❌ DON'T**

```text
git commit -m "AI fixes"
```

**✅ DO**

```text
feat(auth): AI. refactor biometric authentication flow using Koin DI

- Replace direct BiometricPrompt instantiation with BiometricSecretSigner contract
- Add unit tests using FakeBiometricSecretSigner test double
- Verify automated UI test passes on Android API 34 and iOS simulator
- AI-assisted generation reviewed and verified by engineering team
```

*See
[references/rule01.example.md](references/rule01.example.md)
for detailed reference cases.*

______________________________________________________________________

## Rule 2: Pin Dependencies and Enforce Cryptographic Digests

**Description:** Always pin exact library version numbers and lockfile
checksums/digests. Never rely on floating range tags like `latest` or `^1.0.0`.
Pin third-party artifacts to specific cryptographic digests (SHA-256) to
guarantee artifact integrity and prevent supply chain attacks.

**❌ DON'T**

```dockerfile
FROM node:latest
```

**✅ DO**

```dockerfile
FROM node:20.11.1-alpine@sha256:c77dc7458157121287973...
```

*See
[references/rule02.example.md](references/rule02.example.md)
for detailed reference cases.*
