---
name: floo-bug-hunting
description: Hunt for real bugs in a pull request, merge request, branch, local diff or file by tracing concrete inputs through the changed code and the code it reaches, then report only bugs that survive a disproof attempt, as Conventional Comments with a failing input and a fix. Use when the user asks to find bugs, hunt bugs, check correctness, or when floo-run-pr-review launches it as the bug pass of a review.
license: Complete terms in LICENSE
metadata:
  author: github.com/AlonsoFloo
  keywords:
    - AlonsoFloo
    - floo
    - code-review
    - bug-hunting
    - correctness
    - edge-cases
    - conventional-comments
---

# Bug Hunting

Find bugs that will hit a user: a concrete input or state that makes the code
return a wrong result, crash, corrupt data or hang. Nothing else.

## Stance

- **Behavior only.** Do not load style, craftsmanship or taste rules, and do
  not report naming, structure or missing tests. Other passes cover those.
- **Assume there is a bug and go find it.** "No bug found" is a valid result
  only after the [method](#method) ran on every changed function.
- **Inputs are the point.** A bug that needs a specific value, size, state,
  ordering or platform is a real bug. Do not drop it because it "depends on
  the input": find the input and show it.
- **Follow the code.** The diff is the starting point, not the boundary. Read
  the callers of what changed and the functions it calls, including
  pre-existing code: a new call into an old function that breaks on the new
  inputs is a bug of this change.
- **No noise.** Report only a failure you traced from a reachable input to a
  wrong outcome. Do not report "could fail", defensive-code wishes, missing
  validation without a caller that sends the bad value, or code nothing calls.
  One finding per root cause, listing every place it shows.

## Method

### 1. Map the change

For each changed function or block, write down (internally):

- What it takes in: parameters, fields, shared state, I/O results, clock,
  platform, environment, user actions.
- What it produces: return value, state written, data persisted, UI shown,
  events sent.
- Who consumes the output: find the callers, validators, serializers and
  readers with grep or symbol search. Note the contract they expect (units,
  format, nullability, ordering, range).

### 2. Pick hostile inputs

For each input, pick concrete values from
[references/where-bugs-hide.md](references/where-bugs-hide.md) that are most likely to
break this code. Prefer values a real user hits: an empty list, a failed
request, a second tap while loading, data saved by the previous version.

### 3. Trace

Run each hostile input through the code by hand, step by step, across files,
until it reaches a consumer from step 1. Write the actual value at each step.
A bug is a gap between the value that arrives and the value the consumer
expects.

Check both sides of every boundary the change touches: producer and consumer,
`expect` and each `actual`, old and new versions of persisted data, the happy
path and each error path.

### 4. Try to disprove each candidate

Before reporting, try to prove the candidate wrong: a guard upstream, a
type that rules out the input, a caller that never passes it, a test that
already covers it. Keep the candidate only if the disproof fails. When a
link of the trace is a guess (code not available, framework behavior not
verified), say so in the finding instead of dropping it.

## Output

Write each bug with the `floo-conventional-comments` skill, inside a
```` ```markdown ```` fence so it can be pasted into GitHub or GitLab. Use a
four-backtick fence when the comment contains a code block.

A bug is `issue (blocking)` when a user can hit it with a realistic input;
otherwise `issue (non-blocking)`.

````markdown
## Bug hunt: <target>

**Traced:** <each function traced and the hostile inputs tried, one line>
**Result:** <N bugs | No bug found>

### 1. `path/to/File.kt:42` - <short title>

```markdown
<badge> <what breaks, for whom, and why>

**Failing input**

<the concrete input or state, e.g. "the save request fails after the first item">

**Trace**

1. `File.kt:42` computes ...
2. `Validator.kt:17` receives ... and expects ...

Expected: ... Actual: ...

**Suggestion**

<fix, and the regression test that would catch it>
```
````

Order bugs by impact. Report in the chat only; never post on a platform.
