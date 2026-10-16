# Rule 1: Standardize Unit Test Naming Conventions — Reference Examples

Deep dive for [SKILL.md — Rule 1](../SKILL.md).

______________________________________________________________________

## Behavioral Naming Pattern

Using BDD-inspired naming
(`given <context> - on <action> - it should <expected behavior>`) clarifies
expectations when tests fail in CI logs.

### Comprehensive Examples

```kotlin
// Edge cases & domain failure scenarios
@Test
fun `given expired session token - on refresh token - it should throw UnauthorizedException`() { ... }

@Test
fun `given empty search query - on search - it should emit cached default results`() { ... }

@Test
fun `given network disconnection - on order checkout - it should retry 3 times before failing`() { ... }
```
