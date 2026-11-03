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

> Evidence-based review: understand the intent, walk five lenses, report
> actionable Conventional Comments.

Use this skill to review any change: a pull request, a merge request, a branch
against its base, or uncommitted local changes. It works the same whether the
code was written by you, another agent, or a human.

## General Guidelines

### Honesty in Review

- **Don't rubber-stamp.** "LGTM" without evidence of review helps no one. If
  nothing is wrong, say what you checked.
- **Don't soften real issues.** "This might be a minor concern" when it is a bug
  that will hit production is dishonest. Call a bug a bug.
- **Quantify problems when possible.** "This N+1 query adds ~50 ms per item in
  the list" beats "this could be slow".
- **Push back on approaches with clear problems.** Sycophancy is a failure mode
  in reviews. If the implementation has issues, say so directly and propose an
  alternative.
- **Accept override gracefully.** If the author has full context and disagrees,
  defer to their judgment. Comment on code, not people: reframe a personal
  critique ("you forgot") into a statement about the code ("this path does
  not").

### Dead Code Hygiene

Every change can orphan code. After reading the diff, look for:

- Functions, types, parameters, imports, resources, feature flags or config keys
  that are no longer referenced.
- Branches that became unreachable (a `when` case nothing produces anymore, a
  flag that is always true).
- Tests that only exercise code the change removed.
- Commented-out code and `TODO` left behind without an issue reference.

List what you find explicitly with its location. Do not delete anything
yourself during a review: ask the author, e.g. "Should these now-unused
elements be removed: `LegacyMapper`, `Config.oldTimeout`?". Leftover dead code
misleads both future readers and future agents.

### Scope and Evidence

- Review the diff, but read enough surrounding code to judge it: callers,
  implementations of changed interfaces, neighbouring files that show the
  project's conventions.
- Every finding points to a file and line, and is backed by something you read
  or ran. When a claim is a guess, say so.
- Prefer a few precise findings over a long list of vague ones. Merge duplicates
  ("same issue in 4 places: …").
- Don't flag what tooling already enforces (formatter, linter, pre-commit),
  unless the tooling is missing or bypassed.

______________________________________________________________________

## Step 0: Understand the Context

Before looking at code, understand the intent:

```text
- What is this change trying to accomplish?
- What spec or task does it implement?
- What is the expected behavior change?
```

Sources, in order: the PR/MR title and description, linked issues or tickets,
commit messages, the branch name, and the user's own words. Write the intent in
one or two sentences at the top of the report.

If the intent cannot be found, don't invent one: state your best reading and
mark it as an assumption, then add a `question` comment asking for it. A review
without a known intent can only check *how* the code is written, never *whether
it does the right thing*.

Then get the diff and the list of touched files, and note their languages: Step
5 depends on it.

______________________________________________________________________

## Step 1: Software Craftsmanship Rules

Load the `floo-software-craftsmanship` skill and check every rule against the
changed code. Go rule by rule rather than relying on a general impression; the
rules are short enough to check one at a time.

Typical findings: booleans modelling a state machine (Rule 1), an unbounded
`when`/`switch` that will keep growing (Rule 2), mutable state after
construction (Rule 3), reaching through object chains (Rule 5), validation
without a refined type (Rule 7), an unisolated workaround (Rule 9), speculative
abstraction (Rule 11), feature code at top level (Rule 12), callbacks returning
async results (Rule 13).

Also check the change itself for correctness: logic errors, off-by-one, null or
empty handling, error paths that swallow exceptions, race conditions, and
behavior that contradicts the intent from Step 0.

If the change touches CI, Docker, dependencies or commits, also load
`floo-devops-craftsmanship`.

______________________________________________________________________

## Step 2: Taste

Read [references/taste.md](references/taste.md) and check the change against
it. These are Florian's personal conventions for naming, structure, logic,
comments and configuration.

Taste findings are usually `nitpick` or `suggestion` and `non-blocking`. Raise
one to `blocking` only when it hides a real problem (a misleading name that
will cause misuse, for example).

______________________________________________________________________

## Step 3: Security

Read [references/security.md](references/security.md) and walk every item that
applies to the touched code.

For each finding, describe the concrete attack or leak: who can trigger it, with
what input, and what they gain. A security finding without an exploit path is a
`thought`, not an `issue`.

Security issues that can be exploited or that leak secrets or personal data are
always `issue(blocking)`. If the change is large or security-sensitive (auth,
payments, crypto, input parsing, file or shell access), also load the
`security-review` skill for a deeper data-flow pass. Use it only for its
analysis: fold what it finds into this report as Conventional Comments, not into
its own report format.

______________________________________________________________________

## Step 4: Tests

Load the `floo-testing-craftsmanship` skill and check every rule against the
added or changed tests (naming, SETUP/RUN/ASSERT layout, Turbine for flows).

Then challenge whether the tests actually cover the change coherently:

- Map each behavior change from Step 0 to a test. A behavior change with no
  test is a finding.
- Check that each test asserts the behavior its name promises. A test named
  `it should emit error` that only asserts `not null` is a finding.
- Look for the missing cases: error paths, empty and boundary inputs,
  cancellation, concurrency, and the edge case the bug fix was about.
- Check that a bug fix comes with a test that fails without the fix.
- Flag tests that test the mock instead of the code, assert implementation
  details that will break on any refactor, depend on timing (`sleep`, `delay`),
  or share mutable state between tests.
- Flag deleted or weakened assertions and newly skipped tests, and ask why.

If you can run the tests, run them and report the result. If you cannot, say so.

______________________________________________________________________

## Step 5: Language and Platform Skills

The goal of this step is to check that the change is idiomatic and coherent with
the rest of the code base in its language, not only correct.

1. From the touched files, list the languages, frameworks and platform APIs
   involved (e.g. Swift + SwiftUI + async/await, Kotlin + Compose + Flow +
   `WindowInsets`).
1. Load every available skill that matches what the diff actually uses, not
   just its language: a 20-line repository change in Kotlin does not need the
   Compose skills. Use the table below as a starting point, then scan the list of available skills for any other skill whose
   description matches an API or framework the diff uses.
1. Check the change against each loaded skill.
1. Compare with neighbouring code: does the change follow the patterns the code
   base already uses (DI library, navigation, error type, logging, module
   layout)? A new pattern where an established one exists is a finding, even if
   the new pattern is fine in isolation.

| Touched code                                         | Skills to load                                                                        |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Swift, iOS, macOS                                    | `floo-ios-craftsmanship`                                                              |
| `async`/`await`, `Task`, actors, `Sendable`          | `swift-concurrency`                                                                   |
| SwiftUI views and state                              | `swiftui-expert-skill`                                                                |
| `Info.plist`, entitlements, privacy manifest, IAP    | `apple-appstore-reviewer`                                                             |
| Kotlin, Android                                      | `floo-android-craftsmanship`                                                          |
| Kotlin branching (`when`, sealed types, nullables)   | `kotlin-control-flow`                                                                 |
| Coroutines, `Flow`, `StateFlow`, `Channel`           | `kotlin-concurrency-and-flow`                                                         |
| Jetpack Compose UI                                   | `ordering-modifier-chains`, `using-efficient-effects`, `compose-animations`, `styles` |
| Insets, system bars, IME, `Scaffold` padding         | `edge-to-edge`                                                                        |
| Window sizes, tablets, foldables, multi-pane         | `adaptive`                                                                            |
| Navigation                                           | `navigation-3`                                                                        |
| ProGuard / R8 rules                                  | `r8-analyzer`                                                                         |
| Credential Manager, restore keys                     | `restore-credentials`                                                                 |
| JPA / Hibernate entities                             | `kotlin-backend-jpa-entity-mapping`                                                   |
| Gradle, AGP, KMP build, Java → Kotlin                | matching `kotlin-tooling-*` skill                                                     |
| CI, Dockerfile, IaC, dependency manifests, git hooks | `floo-devops-craftsmanship`                                                           |

If no skill matches a language in the diff, say so in the report and review it
against the conventions of the surrounding code.

______________________________________________________________________

## Output

Load the `floo-conventional-comments` skill and use its badge format for every
comment, in the report and on the PR. In plain terminal output where markdown
does not render, use the text label instead (`issue(blocking):`). Each comment
carries a type and, when merge impact matters, a decoration (`blocking`,
`non-blocking`, `if-minor`).

Choose the type honestly:

- `issue`: something is wrong. `blocking` for correctness, security, data loss,
  broken build or failing tests.
- `suggestion`: a concrete better alternative.
- `nitpick`: taste and polish.
- `question`: you need context before you can judge.
- `todo`, `chore`: small required follow-ups.
- `thought`: an idea, not a request.
- `praise`: something done well. Use it when it is true, not as padding.

Rules for each comment:

- Locate it: `path/to/File.kt:42` (or a line range).
- Say what is wrong and why it matters, in one or two sentences.
- **Every `issue` about behavior has reproducible steps**: the input, state or
  sequence that triggers the problem, and what happens versus what should
  happen. For a security issue, the attack path. If you could not run it, say
  you reasoned it from the code.
- A design or rule finding (a craftsmanship or taste rule) has an **Impact**
  line instead: the concrete situation in which it will hurt. Don't invent a
  reproduction for it. It is an `issue` only when that impact is a real defect
  or risk today; otherwise it is a `suggestion`.
- **Give a suggestion whenever possible**: a code snippet or a precise
  description of the fix. Prefer a GitHub `suggestion` code block when posting
  inline on a PR and the fix is a direct replacement of the commented lines.

### Report template

```md
## Review: <PR title or change summary>

**Intent:** <one or two sentences from Step 0, or "Assumed: …">
**Scope:** <N files, languages, skills loaded in Step 5>
**Verdict:** <Approve | Request changes | Comment> - <one-line reason>

| Step              | Result                         |
| ----------------- | ------------------------------ |
| 1. Craftsmanship  | <n findings / ✓ checked rules> |
| 2. Taste          | ...                            |
| 3. Security       | ...                            |
| 4. Tests          | ...                            |
| 5. Language       | ...                            |

### Findings

<badge> **`path/File.kt:42`** - <title>

<what is wrong and why it matters>

**Steps to reproduce**
1. ...
2. ...
Expected: ... / Actual: ...
(or, for a design or rule finding: **Impact** <when it will hurt>)

**Suggestion**
<code or precise fix>

---

### Dead code

<list, or "None found">

### What I checked and could not check

<tests run, things that need a device, missing context>
```

Order findings by severity: blocking first, then non-blocking, then nitpicks
and praise. Use `Request changes` when at least one finding is `blocking`.

### Where to post

- Local diff or when the user asks for a report: print the report.
- Pull request: print the report, and post it on the PR only when the user asks.
  When posting, put each finding as an inline comment on its line, and the
  summary (intent, verdict, step table, dead code) as the review body. Never
  approve or merge on the user's behalf.

## Bundled Resources

| Resource                                         | When to load | Why                                                        |
| ------------------------------------------------ | ------------ | ---------------------------------------------------------- |
| [references/taste.md](references/taste.md)       | Step 2       | Florian's naming, structure, logic and config conventions. |
| [references/security.md](references/security.md) | Step 3       | Security checklist to walk on every review.                |
