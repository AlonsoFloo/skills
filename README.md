# Skills

A personal, agent-friendly repository of engineering rules, patterns, and practices for software development.

The repository is designed around the **Agent Skills** convention: each skill is a focused directory containing a `SKILL.md` with the instructions an AI coding assistant or reviewer can load when the domain is relevant.

## Install via API

```bash
apm install AlonsoFloo/skills
```

## Design principles

### 1. One domain per skill

A skill should answer one question well. Keep framework-specific advice out of the core skill unless the rule genuinely depends on that framework.

### 2. Rules are explicit

Prefer:

- a short rule statement
- why the rule exists
- `❌ DON'T`
- `✅ DO`

This makes the material useful to both humans and coding agents.

### 3. Examples matter

Examples should demonstrate the intended design, not merely describe it.

### 4. Skills should be composable

A task can use several skills at once. For example, an Android feature may need:

`core-engineering` + `code-logic` + `testing` + `android-kotlin`

### 5. Prefer evidence over dogma

Rules are defaults, not substitutes for engineering judgment. A documented exception is better than a clever violation that nobody understands.

## How to use the skills

For an AI coding assistant that supports the Agent Skills convention, make the `skills/` directory available as the assistant's skills source.

For agents without automatic discovery, explicitly provide the relevant `SKILL.md` files as context.

For a human developer, the same files are intended to work as a searchable engineering handbook.
