# Taste

Florian's personal conventions. They complement the craftsmanship skills (do not
re-check those rules here) and cover what a linter cannot see. Each item is a
preference, not a law.

## Naming

- Names reveal intent and use the domain's words: `PaymentStrategy`,
  `NonEmptyList`, `DownloadState`, not `Manager`, `Helper`, `Utils`, `Data`,
  `Info`.
- Describe behavior, not implementation: `formatName()` over
  `concatFirstAndLastName()`.
- Booleans read as a question: `isEnabled`, `hasAccess`, `shouldRetry`.
- Avoid abbreviations except universally known ones (`id`, `url`, `db`).
- Everything in the code base is written in English: names, comments, docs,
  commit messages.

## Logic and Structure

- Edit the source of truth, never a generated copy (e.g. `.apm/` and not
  `.claude/` or `.agents/`; the boilerplate and not each repo's copy). A
  generated file changed by hand is a finding.
- Reuse what the code base already has (libraries, helpers, patterns) before
  adding something new.
- Exhaustive `when` / `switch` over sealed types, no `else` that silently
  swallows new cases.
- Comments explain *why*, not *what*. Restating the code is noise.
