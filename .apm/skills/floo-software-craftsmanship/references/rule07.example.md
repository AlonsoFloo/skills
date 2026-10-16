# Rule 7: Parse, Don't Validate — Reference Examples

Deep dive for [SKILL.md — Rule 7](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

Shotgun parsing happens when input checks are scattered across functions,
requiring repeated assertions. Formal parsing converts input into strongly typed
domain objects once at boundary entry points.

### Comprehensive Example

**❌ DON'T (Validating repeatedly across callers)**

```kotlin
fun processEmail(rawEmail: String) {
    if (!rawEmail.contains("@")) error("Invalid email")
    sendNotification(rawEmail)
}

fun sendNotification(rawEmail: String) {
    if (!rawEmail.contains("@")) error("Invalid email again") // Defensive duplicate check
}
```

**✅ DO (Parsing once into Value Object statically guaranteeing validity)**

```kotlin
@JvmInline
value class EmailAddress private constructor(val value: String) {
    companion object {
        fun parse(raw: String): Result<EmailAddress> {
            return if (raw.contains("@") && raw.length >= 5) {
                Result.success(EmailAddress(raw))
            } else {
                Result.failure(IllegalArgumentException("Invalid email format: $raw"))
            }
        }
    }
}

fun sendNotification(email: EmailAddress) {
    // Compiler guarantees email is valid; zero assertions required here!
}
```
