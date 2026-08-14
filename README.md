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

## Troubleshooting

### `release-please` error: "GitHub Actions is not permitted to create or approve pull requests"

This error occurs when the default `GITHUB_TOKEN` is restricted from creating or approving pull requests by repository settings or organization policy.

To resolve this issue:

1. In your GitHub repository, go to **Settings** > **Actions** > **General**.
1. Under **Workflow permissions**, select **Read and write permissions**.
1. Check the box for **Allow GitHub Actions to create and approve pull requests**.
1. Click **Save**.

If organization policies prohibit enabling this setting for `GITHUB_TOKEN`, you can create a Personal Access Token (PAT) with `repo` scopes (or use a GitHub App token) and add it to your repository secrets as `RELEASE_PLEASE_TOKEN`.
