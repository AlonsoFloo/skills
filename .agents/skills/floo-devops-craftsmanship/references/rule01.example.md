# Rule 1: Enforce Detailed Commit Messages for AI-Assisted Code — Reference Examples

Deep dive for [SKILL.md — Rule 1](../SKILL.md).

______________________________________________________________________

## AI Commits Audit Format

To maintain clear engineering history when using AI tools, commit bodies must
explain changes thoroughly.

```text
refactor(database): AI. migrate Room database schema to v4 with auto-migration

- Generated AutoMigrationSpec from v3 to v4 adding user_preferences table
- Updated UserDatabase test suite to verify migration integrity against schema JSON
- Executed local migration benchmarks ensuring <15ms execution
- AI generated code reviewed and verified by database guild
```
