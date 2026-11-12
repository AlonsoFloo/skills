---
argument-hint: '[PR number, PR URL or branch]'
description: Review a pull request, branch or local diff with three passes (bugs,
  security, quality) and merge them into one Conventional Comments report
---

# Run PR Review

Review the target the user gives: a pull request number or URL, a branch, or,
when nothing is given, the current branch against its base plus any
uncommitted changes.

## 1. Prepare

Collect what every pass needs: the diff, the list of changed files, the PR
title and description, linked issues and commit messages. Pass all of it to
each pass so none of them has to fetch it again.

## 2. Run the Three Passes

Run each pass in its own subagent, in parallel, when subagents are available
(Claude Code `Agent`/`Task` tool, or an equivalent). Each subagent loads only
the skills of its pass, so the rules of one pass do not crowd out the others.
Without subagents, run the passes one after the other in this order.

1. **Bugs:** the `floo-bug-hunting` skill on the diff.
1. **Security:** the `security-review` skill, scoped to the changed files and
   the code they reach. Skip its dependency audit unless the diff changes a
   dependency. Return findings only: no patch is applied.
1. **Quality:** the `floo-code-review-and-quality` skill on the diff.

Each pass returns its own report.

## 3. Consolidate

Merge the three reports into one, written with the `floo-conventional-comments`
skill:

- **Deduplicate.** When two passes report the same problem at the same place,
  keep one finding and name both passes.
- **Map severities.** A `security-review` CRITICAL or HIGH finding is
  `issue (blocking)`; MEDIUM is `issue (non-blocking)`; LOW and INFO are
  `suggestion (non-blocking)` or dropped when they are noise.
- **Order** blocking findings first, then by impact.
- Keep each pass's reproduction steps, failing input and suggestion.

````markdown
## Review: <PR title>

**Intent:** <one sentence>
**Verdict:** <Approve | Request changes> - <one-line reason>
**Passes:** Bugs <N findings, traced: ...> · Security <N> · Quality <N, rules checked: ...>

### 1. `path/to/File.kt:42` - <short title> (<pass>)

```markdown
<comment from the pass>
```

### 2. ...
````

Use `Request changes` when at least one finding is blocking. Report in the chat
only; never post on GitHub, GitLab or any other platform.