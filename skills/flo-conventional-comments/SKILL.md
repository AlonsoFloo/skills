---
name: flo-conventional-comments
description: Use whenever commenting on a pull request or merge request, writing PR review feedback, creating Conventional Comments badge markdown, or choosing blocking/non-blocking comment decorations.
license: Complete terms in LICENSE
metadata:
  author: github.com/AlonsoFloo
  keywords:
  - AlonsoFloo
  - flo
  - conventional-comments
  - code-review
  - pull-request
  - shields-io
  - badges
---

# Conventional Comments

## Overview

Conventional Comments badges are linked `shields.io` badges that encode a comment type and optional decoration. Use the exact labels, colors, URL shape, and query parameters below; small syntax changes produce different badges or lose comment metadata.

## When to Use

Use when writing or generating:

- Conventional Comments badge markdown.
- `shields.io` tags for `praise`, `nitpick`, `suggestion`, `todo`, `issue`, `question`, `thought`, or `chore`.
- Review comment labels with `non-blocking`, `blocking`, or `if-minor`.
- Documentation, tests, examples, or generated comments that must match the browser extension output.

## When NOT to Use

Skip when:

- The user wants plain Conventional Comments text such as `suggestion(blocking):`.
- The target project has its own badge syntax unrelated to this badge format.
- The user explicitly asks for generic Shields.io badges without metadata links.

## Output Format

Use one of these exact forms:

```md
[![<type>](https://img.shields.io/badge/<type>-<typeColor>)](https://comments.example/cc?l=<type>)
[![<type>(<decoration>)](https://img.shields.io/badge/<type>-<escapedDecoration>-<decorationColor>?labelColor=<typeColor>)](https://comments.example/cc?l=<type>&d=<decoration>)
```

The extension inserts a trailing space after the closing link, and in prettified mode adds a newline after that prefix. For normal documentation examples, show the visible markdown without relying on the invisible trailing space.

Encoding rules:

- Type and decoration values in image alt text stay literal: `issue(non-blocking)`.
- In the Shields path only, replace `-` with `--`: `non-blocking` becomes `non--blocking`, `if-minor` becomes `if--minor`.
- Metadata query parameters stay literal and URL-encoded normally: `?l=issue&d=non-blocking`.
- Undecorated badges do not use `labelColor`.
- Decorated badges use the decoration color as the right-side badge color and the type color as `labelColor`.

## Type Reference

| Type         | Use When                                                                                              | Color    |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- |
| `praise`     | Highlighting something positive.                                                                      | `28A745` |
| `nitpick`    | Minor feedback, style, naming, or polish. Usually non-blocking unless explicitly decorated otherwise. | `F59E0B` |
| `suggestion` | Proposing a concrete improvement or alternative.                                                      | `3B82F6` |
| `todo`       | Marking something that needs to be done.                                                              | `E879F9` |
| `issue`      | Pointing out a problem. Usually use `blocking` only if it must be fixed before merge.                 | `EF4444` |
| `question`   | Asking for clarification or missing context.                                                          | `8B5CF6` |
| `thought`    | Sharing an idea, reflection, or optional consideration.                                               | `6B7280` |
| `chore`      | Requesting a minor non-code or housekeeping task.                                                     | `F97316` |

## Decoration Reference

| Decoration     | Use When                                                                                                                                         | Color      |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- |
| none           | The type alone communicates enough, or merge impact is intentionally unspecified.                                                                | Type color |
| `non-blocking` | The comment is optional and should not block merge.                                                                                              | `9CA3AF`   |
| `blocking`     | The comment must be addressed before merge. Use for correctness, security, data loss, broken builds, failed tests, or required product behavior. | `374151`   |
| `if-minor`     | The comment is worth addressing only if the effort is small.                                                                                     | `14B8A6`   |

Blocking is never implied by the type. `issue` without `(blocking)` is only `issue`; `nitpick` without `(non-blocking)` is only `nitpick`.

## Complete Markdown Matrix

For exact markdown, load `references/matrix.md`. It contains all 32 combinations: 8 types times none, `non-blocking`, `blocking`, and `if-minor`.

Use the matrix instead of regenerating from memory when the user asks for a complete table, exact output, or copy-ready markdown.

## Examples

<good-example>
User asks for an issue that blocks merge:

```md
[![issue(blocking)](https://img.shields.io/badge/issue-blocking-374151?labelColor=EF4444)](https://comments.example/cc?l=issue&d=blocking)
```

The Shields right-side color is the `blocking` color, and the left label color is the `issue` color.
</good-example>

<bad-example>

```md
[![issue(blocking)](https://img.shields.io/badge/issue-blocking-EF4444)](https://comments.example/cc?l=issue(blocking))
```

**Why bad:** It uses the issue color for the decoration, omits `labelColor`, and puts the decoration inside `l` instead of `d`.
</bad-example>

## Common Mistakes

| Mistake                                                              | Fix                                                                                                    |
| -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Producing only 8 badges.                                             | Include all 32 combinations: each type with no decoration, `non-blocking`, `blocking`, and `if-minor`. |
| Omitting `todo`.                                                     | Include `todo`; it exists in the implementation even if older docs omit it.                            |
| Writing `nonblocking`, `non blocking`, `if minor`, or `minor`.       | Use exact decorations: `non-blocking`, `blocking`, `if-minor`.                                         |
| Using `non-blocking` or `if-minor` directly inside the Shields path. | Use `non--blocking` and `if--minor` in the Shields path only.                                          |
| Doubling hyphens in alt text or metadata query params.               | Keep alt text and metadata params literal: `non-blocking`, `if-minor`.                                 |
| Outputting only the image markdown.                                  | Wrap it in the metadata link: `[![...](...)](https://comments.example/cc?...)`.                        |
| Using `?label=...&decoration=...` or `?type=...`.                    | Use `?l=<type>` and optional `&d=<decoration>`.                                                        |
| Adding `labelColor` to undecorated badges.                           | Use `labelColor` only for decorated badges.                                                            |
| Assuming `issue` means blocking or `nitpick` means non-blocking.     | Add the decoration explicitly when merge impact matters.                                               |

## Verification

Before using a generated table as source material, check:

- Exactly 32 rows for combinations.
- All 8 type colors are present.
- All 3 decoration colors are present.
- Every decorated badge has `?labelColor=<typeColor>`.
- No undecorated badge has `labelColor`.
- `non--blocking` and `if--minor` appear in Shields URLs, but not in alt text or metadata query params.

## Bundled Resources

| Resource               | When to Load                                        | Why                                             |
| ---------------------- | --------------------------------------------------- | ----------------------------------------------- |
| `references/matrix.md` | When exact markdown or all combinations are needed. | Contains the complete 32-row copy-ready matrix. |
