---
name: floo-code-review-and-quality
description: Review a pull request, merge request, branch or local diff in five steps (craftsmanship rules, Florian's taste, security, tests, language-specific skills) and report findings as Conventional Comments with reproduction steps and suggestions. Use whenever the user asks to review code, review a PR or MR, audit a diff before merge, check code quality, or says "review", "code review", "PR review", "relis ce code", even if they do not name this skill.
license: Complete terms in LICENSE
metadata:
  author: github.com/AlonsoFloo
  keywords:
    - AlonsoFloo
    - floo
    - code-review
    - pull-request
    - quality
    - security
    - conventional-comments
---

# Code Review and Quality

## General Guidelines

### Honesty in Review

When reviewing code — whether written by you, another agent, or a human:

- **Don't rubber-stamp.** "LGTM" without evidence of review helps no one.
- **Don't soften real issues.** "This might be a minor concern" when it's a bug
  that will hit production is dishonest.
- **Quantify problems when possible.** "This N+1 query will add ~50ms per item
  in the list" is better than "this could be slow."
- **Push back on approaches with clear problems.** Sycophancy is a failure mode
  in reviews. If the implementation has issues, say so directly and propose
  alternatives.
- **Accept override gracefully.** If the author has full context and disagrees,
  defer to their judgment. Comment on code, not people — reframe personal
  critiques to focus on the code itself.

### Dead Code Hygiene

Every change can orphan code. Take this into consideration when doing the
review.

### Scope

- Read enough surrounding code to judge the diff (callers, implementations,
  neighbouring files).
- Every finding points to a file and line. When a claim is a guess, say so.
- Don't flag what tooling already enforces (formatter, linter, pre-commit).

## Step 0: Understand the Context

Before looking at code, understand the intent:

```text
- What is this change trying to accomplish?
- What spec or task does it implement?
- What is the expected behavior change?
```

Use the PR description, linked issues, commits and branch name. If the intent is
unclear, state your assumption and ask a `question`.

## Step 1: Software Craftsmanship

Load `floo-software-craftsmanship` and check every rule against the change, plus
correctness against the intent from Step 0.

## Step 2: Taste

Check the change against [references/taste.md](references/taste.md).

## Step 3: Security

Check the change against [references/security.md](references/security.md).
Describe the attack path for each finding.

## Step 4: Tests

Load `floo-testing-craftsmanship` and check every rule against the tests. Then
challenge their coherence: each behavior change from Step 0 has a test, each
test asserts what its name promises, edge and error cases are covered, and a bug
fix has a test that fails without it.

## Step 5: Language and Platform

From the touched files, identify the languages, frameworks and APIs used. Check
your available skills and load every one that matches what the diff uses (e.g.
`floo-ios-craftsmanship`, `floo-android-craftsmanship`, `swift-concurrency`,
`edge-to-edge`). Then check that the change is idiomatic and coherent with the
patterns the code base already uses.

## Output

Report in the chat only. Never post on GitHub, GitLab or any other platform.

Write each comment with the `floo-conventional-comments` skill. Each comment:

- Has reproducible steps when it is an `issue`.
- Has a suggestion when possible.

Write each comment inside a ```` ```markdown ```` fence so it can be copied
as-is into GitHub or GitLab. Use a four-backtick fence when the comment itself
contains a code block.

### Report template

````markdown
## Review: <PR title>

**Intent:** <one sentence from Step 0>
**Verdict:** <Approve | Request changes> - <one-line reason>

### 1. `path/to/File.kt:42` - <short title>

```markdown
<badge> <what is wrong and why it matters>

**Steps to reproduce**

1. ...
2. ...

Expected: ... Actual: ...

**Suggestion**

<fix>
```

### 2. ...
````

Order findings blocking first. Use `Request changes` when at least one finding
is blocking.
