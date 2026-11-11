---
name: floo-code-review-and-quality
description: Review a pull request, merge request, branch or local diff in six steps (correctness, craftsmanship rules, Florian's taste, security, tests, language-specific skills) and report findings as Conventional Comments with reproduction steps and suggestions. Use whenever the user asks to review code, review a PR or MR, audit a diff before merge, check code quality, or says "review", "code review", "PR review", "relis ce code", even if they do not name this skill.
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

### Signal Over Noise

Every finding must be worth the author's time. Report a rule violation only when
you can name what it costs in this change: a bug, a race, code that cannot be
tested, or a pattern a new public API would lock in. If you cannot name the
cost, drop it. A review with three findings that matter beats one with fifteen
that the author has to sort.

- Judge the lines the diff adds or changes. Pre-existing code is in scope only
  when the diff makes it worse.
- One comment per rule, listing every location, instead of one per line.
- A rule violation is `issue (blocking)` only when it causes a bug or locks a
  bad pattern into a public API. Otherwise it is a non-blocking `suggestion`.

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

## Step 1: Correctness

Check that the change does what Step 0 says it should: logic errors, unhandled
failures, leaked resources, state left inconsistent on error, concurrency bugs.

## Step 2: Software Craftsmanship

Load `floo-software-craftsmanship` and go through its rules one by one. For each
rule, look for its "Spot it in review" signal in the changed lines. Most rules
will not apply to a given diff, and that is expected: the walk exists so that no
rule is skipped, not so that each one produces a comment. Read the rule's
reference file before reporting a borderline case, and report only what passes
[Signal Over Noise](#signal-over-noise).

## Step 3: Taste

Check the change against [references/taste.md](references/taste.md).

## Step 4: Security

Check the change against [references/security.md](references/security.md).
Describe the attack path for each finding.

## Step 5: Tests

Load `floo-testing-craftsmanship` and go through its rules against the tests the
same way as in Step 2. Then
challenge their coherence: each behavior change from Step 0 has a test, each
test asserts what its name promises, edge and error cases are covered, and a bug
fix has a test that fails without it.

## Step 6: Language and Platform

From the touched files, identify the languages, frameworks and APIs used. Check
your available skills and load every one that matches what the diff uses (e.g.
`floo-ios-craftsmanship`, `floo-android-craftsmanship`, `swift-concurrency`,
`edge-to-edge`). Go through the rules of each loaded skill the same way as in
Step 2. Then check that the change is idiomatic and coherent with the patterns
the code base already uses.

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
**Rules checked:** <each rule skill walked, e.g. craftsmanship 1-13, testing 1-3, android 1-3>

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
