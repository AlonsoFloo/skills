# Skills

A personal, agent-friendly repository of engineering rules, patterns, and
practices for software development.

The repository is designed around the **Agent Skills** convention: each
craftsmanship skill is a focused directory containing a concise `SKILL.md` (with
1 example per rule) and a `references/` subfolder with additional deep-dive
examples.

## Install via APM

<!-- x-release-please-start-version -->

```bash
apm install https://github.com/AlonsoFloo/skills.git#v1.0.0
```

## Available Skills

- **`software-craftsmanship`**: Software architecture, domain modeling,
  pragmatic code logic & design principles.
- **`testing-craftsmanship`**: Standardized behavioral unit testing and reactive
  stream testing.
- **`android-craftsmanship`**: Modern Android UDF architecture, UI state
  management & Jetpack Compose best practices.
- **`ios-craftsmanship`**: Swift memory safety, retain cycle prevention &
  SwiftUI best practices.
- **`devops-craftsmanship`**: AI commit message standards and dependency
  checksum pinning.

## Design principles

### 1. One domain per skill

A skill answers one domain well. Keep framework-specific advice inside dedicated
language/platform skills.

### 2. Numbered & explicit rules with light SKILL.md

Rules are explicitly numbered starting from `Rule 1` in each skill. `SKILL.md`
contains 1 concise example per rule to stay light, while detailed edge cases and
deep dives reside in `references/examples.md`.

### 3. APM Integration

Integrated dependencies like Anthropic's `skill-creator` are declared in
`apm.yml`.

### 4. Composability

Tasks can compose multiple skills (e.g. `software-craftsmanship` +
`testing-craftsmanship` + `android-craftsmanship`).
