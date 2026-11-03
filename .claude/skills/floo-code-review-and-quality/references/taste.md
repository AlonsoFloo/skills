# Taste

Florian's personal conventions. They complement the craftsmanship skills (do not
re-check those rules here) and cover what a linter cannot see. Each item is a
preference, not a law: flag a deviation as `nitpick` or `suggestion`, unless it
hides a real problem.

## Change Shape

- Keep changes simple and surgical: the diff does what the intent says and
  nothing else. Drive-by refactors, renames and reformatting belong in their own
  change.
- One concern per pull request and per commit. A PR mixing a feature, a
  dependency bump and a cleanup should be split.
- Edit the source of truth, never a generated copy (e.g. `.apm/` and not
  `.claude/` or `.agents/`; the boilerplate and not each repo's copy). A
  generated file changed by hand is a finding.
- Reuse what the code base already has (libraries, helpers, patterns) before
  adding something new. Never assume a library is available: check the manifest.

## Naming

- Names reveal intent and use the domain's words: `PaymentStrategy`,
  `NonEmptyList`, `DownloadState`, not `Manager`, `Helper`, `Utils`, `Data`,
  `Info`.
- Describe behavior, not implementation: `formatName()` over
  `concatFirstAndLastName()`.
- Booleans read as a question: `isEnabled`, `hasAccess`, `shouldRetry`.
- Constants are `SCREAMING_SNAKE_CASE` and scoped (`private const val MAX_RETRIES = 3`), never magic numbers inline.
- Files, skills, branches and commands use `kebab-case` with a clear prefix
  (`floo-…`, `feature/…`, `fix/…`).
- Test names follow `given <context> - on <action> - it should <expected>`.
- Avoid abbreviations except universally known ones (`id`, `url`, `db`).
- Everything in the code base is written in English: names, comments, docs,
  commit messages.

## Logic and Structure

- Guard clauses and early returns over nested `if`. Validate arguments first and
  fail with a clear message (shell: print `Usage : …` and return).
- Exhaustive `when` / `switch` over sealed types, no `else` that silently
  swallows new cases.
- Explicit types at API boundaries (public functions, properties, return
  types).
- Immutable by default (`val`, `let`, read-only collections). Copy defensively
  when exposing internal collections (`toMap()`, `toList()`).
- Behavior lives with the type it belongs to: extensions and members rather than
  free functions taking the type as first parameter.
- Small functions doing one thing. A function that needs a comment to separate
  its parts wants to be split.
- Dependencies are injected and named (interface / protocol / `fun interface`),
  never reached through singletons or global state.
- Explicit defaults with an override, rather than required configuration
  (`base="main"` unless an argument is given).
- Fail loudly on programmer errors, degrade gracefully on environment issues
  (skip the step and say so, as `setup.sh` does without sudo).
- Back up before overwriting user data (`.bak` copies), never destroy silently.

## Comments and Documentation

- Comments explain *why*, not *what*. Restating the code is noise.
- Workarounds carry the reason, the upstream issue link and the removal
  condition (e.g. "APM bug microsoft/apm#3391, revert when fixed").
- Pinned versions carry a trailing comment with the human-readable version
  (`#5bda39f… # v1.0.15`).
- No commented-out code and no `TODO` without an issue reference.
- A README keeps the usual shape: Purpose, Prerequisites, Installation,
  Contribution setup, Structure, References. A user-visible change updates it.
- Public APIs and skills have a one-line description that says when to use them.

## Configuration and Tooling

- Respect `.editorconfig`: LF, final newline, no trailing whitespace, 4 spaces
  (2 for YAML, Markdown, JSON, XML).
- Every dependency, action, image and tool is pinned to an exact version or
  digest, with automated updates (Renovate) rather than floating tags.
- Pre-commit hooks pass. A change that disables, skips or loosens a hook or a CI
  check needs a written reason.
- Secrets and machine-specific values live outside the repository (`untracked/`,
  environment, secret store), with a committed `*_exemple` template if useful.
- Shell scripts: `#!/bin/bash` shebang, quoted variables (`"$file"`),
  `[[ … ]]` tests, ShellCheck clean.

## Git

- Conventional Commits (`feat:`, `fix:`, `refactor:`, `docs:`, `chore:`, with a
  scope when useful), and Conventional Branch names (`feature/`, `bugfix/`,
  `hotfix/`, `release/`, `chore/`).
- AI-assisted commits are prefixed `AI.` and their body explains intent,
  reasoning, touched areas and tests run.
- The PR description follows the repository template (Description,
  Dependencies) and links the related issue or PR.
