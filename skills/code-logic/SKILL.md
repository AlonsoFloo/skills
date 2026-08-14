---
name: code-logic
description: Code logic, patterns & pragmatic engineering
---

# Code Logic

> Code logic, patterns & pragmatic engineering

This skill is derived from the **Core Engineering Skills** specification supplied with this repository.
Treat the rules below as engineering guidance for AI coding assistants, code reviewers, and engineers.
When a rule is context-dependent, prefer explicit domain reasoning over mechanical application.

## Rule 07: Parse, Don't Validate

> *"Write parsers, not validators."* — Alexis King

**Description:** Validation checks a predicate without refining the underlying data type, forcing downstream functions to re-check validity ("shotgun parsing"). Parsing transforms an unrefined type into a refined domain type at system boundaries. The refined type acts as a static proof of validity.

**❌ DON'T (Validating: Type remains unrefined)**

```kotlin
fun validateListIsNotEmpty(items: List<Item>) {
    if (items.isEmpty()) throw IllegalArgumentException("List must not be empty")
}

fun processFirstItem(items: List<Item>): Item {
    validateListIsNotEmpty(items)
    return items.first()
}
```

**✅ DO (Parsing: Transform raw type into refined domain type)**

```kotlin
data class NonEmptyList<out T>(val head: T, val tail: List<T>) {
    companion object {
        fun <T> parse(items: List<T>): Result<NonEmptyList<T>> =
            if (items.isNotEmpty()) Result.success(NonEmptyList(items.first(), items.drop(1)))
            else Result.failure(IllegalArgumentException("List cannot be empty"))
    }
}

fun processFirstItem(items: NonEmptyList<Item>): Item {
    return items.head // No checks needed, compiler guarantees non-emptiness statically!
}
```

## Rule 08: Postel's Law (Robustness Principle)

> *"Be conservative in what you do, be liberal in what you accept from others."*

**Description:**

1. **Be Conservative (Producers):** Output strictly validated, spec-compliant data. Never emit inconsistent types.
1. **Be Liberal (Consumers):** Parse inputs forgivingly. Ignore unknown fields and provide sensible defaults.

**❌ DON'T (Rigid Consumer & Non-compliant Producer)**

```kotlin
@Serializable
data class UserResponse(
    val id: String,
    val username: String, // Crashes if backend returns unknown fields!
)
```

**✅ DO (Forgiving Consumer & Strict Producer)**

```kotlin
val jsonConfig = Json {
    ignoreUnknownKeys = true // Safely ignores newly added backend fields
    coerceInputValues = true // Coerces nulls gracefully
}

@Serializable
data class UserResponse(
    val id: String,
    val username: String,
    val email: String = "", // Default fallback
    val status: UserStatus = UserStatus.UNKNOWN, // Fallback for newly added enum cases
)
```

## Rule 09: Restrain Extension Function Overuse

**Description:** Adding extension methods everywhere pollutes IDE autocompletion and obscures core class boundaries. Do not turn internal implementation details into global extension functions.

**❌ DON'T**

```kotlin
fun String.formatMySpecificInternalDatabaseKey(): String = "DB_KEY_$this"
```

**✅ DO**

```kotlin
internal object DatabaseKeyFormatter {
    fun formatKey(rawKey: String): String = "DB_KEY_$rawKey"
}
```

## Rule 10: Isolate Quirky Workarounds in Dedicated Spaces

**Description:** When a hack, dynamic layout adjustment, or vendor workaround is strictly unavoidable, isolate it in a dedicated file (e.g. `shame.css` or `Workarounds.kt`) and document the exact reason, bug ticket reference, and condition for removal.

**❌ DON'T**

```css
.btn-primary {
  margin-top: -3px; /* fix alignment on Safari */
}
```

**✅ DO**

```css
/* Located in shame.css */
/* WORKAROUND (Safari 17 flexbox bug #12948): Offset button alignment */
.safari-legacy .btn-primary {
  margin-top: -3px;
}
```

## Rule 11: Knuth's Optimization Principle

> *"Premature optimization is the root of all evil (or at least most of it) in programming."*

**Description:** Write clean, readable, correct code first. Do not add complex caching, manual memory hacks, or esoteric algorithms until empirical benchmarks (profilers, trace logs) prove a performance bottleneck exists.

## Rule 12: YAGNI (You Aren't Gonna Need It)

**Description:** Always implement features when you actually need them, never when you just foresee that you *might* need them. Unused abstraction layers add complexity, maintenance burden, and surface area for bugs.

______________________________________________________________________

## 🧪 DOMAIN: TESTS
