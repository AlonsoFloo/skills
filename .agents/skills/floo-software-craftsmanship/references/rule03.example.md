# Rule 3: Enforce Immutability Post-Construction — Reference Examples

Deep dive for [SKILL.md — Rule 3](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

Mutable domain objects or exposed mutable state enable unexpected mutations
across thread boundaries or layer boundaries. Post-construction state freezing
ensures predictable behavior.

### Comprehensive Example

**❌ DON'T (Exposing mutable collections post-construction)**

```kotlin
class UserProfile(val id: String) {
    val roles: MutableList<String> = mutableListOf() // External callers can clear or modify this list anytime!
}
```

**✅ DO (Defensive copying and unmodifiable state encapsulation)**

```kotlin
class UserProfile private constructor(
    val id: String,
    private val _roles: List<String>
) {
    val roles: List<String> get() = _roles // Exposed as read-only interface

    class Builder(private val id: String) {
        private val roles = mutableListOf<String>()

        fun addRole(role: String) = apply { roles.add(role) }

        fun build(): UserProfile {
            require(id.isNotBlank()) { "ID required" }
            return UserProfile(id, roles.toList()) // Unmodifiable snapshot
        }
    }
}
```
