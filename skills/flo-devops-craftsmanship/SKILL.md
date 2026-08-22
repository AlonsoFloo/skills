---
name: flo-devops-craftsmanship
description: DevOps, AI-assisted development conventions & supply-chain security
license: Complete terms in LICENSE
metadata:
  author: github.com/AlonsoFloo
  keywords:
    - AlonsoFloo
    - flo
    - devops
    - ci-cd
    - supply-chain-security
    - git-commit
    - dependencies
---

# DevOps Craftsmanship

> DevOps, AI-assisted development conventions & supply-chain security

Treat the rules below as engineering guidance for AI coding assistants, code
reviewers, and software engineers. For comprehensive examples and deep dives,
see [references/examples.md](references/examples.md).

You are never allow to commit change automaticaly. Leave the final decision to the human developer.
Leave the changes in the staging area for review and approval before committing, unless explicitly authorized.

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
[references/examples.md#rule-1-enforce-detailed-commit-messages-for-ai-assisted-code](references/examples.md#rule-1-enforce-detailed-commit-messages-for-ai-assisted-code)
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
[references/examples.md#rule-2-pin-dependencies-and-enforce-cryptographic-digests](references/examples.md#rule-2-pin-dependencies-and-enforce-cryptographic-digests)
for detailed reference cases.*
