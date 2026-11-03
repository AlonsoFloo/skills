---
argument-hint: '[PR number, PR URL or branch]'
description: Review a pull request, branch or local diff with the floo-code-review-and-quality
  skill and report Conventional Comments
---

# Run PR Review

Load the `floo-coder` agent and execute the `floo-code-review-and-quality` skill
on the target the user gives: a pull request number or URL, a branch, or, when
nothing is given, the current branch against its base plus any uncommitted
changes.

Follow every step of the skill (context, craftsmanship, taste, security, tests,
language skills) and print the report. Post the comments on the pull request
only if the user asks for it.