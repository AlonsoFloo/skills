# Rule 1: Avoid Booleans for Complex State or Results — Reference Examples

Deep dive for [SKILL.md — Rule 1](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

When representing UI state or multi-step workflow outcomes, using independent
Boolean properties creates invalid state combinations ($2^N$ states for $N$
boolean variables).

### Comprehensive Example

**❌ DON'T (Boolean explosion leading to impossible states)**

```kotlin
data class OrderState(
    val isPending: Boolean = false,
    val isProcessing: Boolean = false,
    val isCompleted: Boolean = false,
    val isFailed: Boolean = false,
    val errorMessage: String? = null
)
// Allows impossible combinations like:
// OrderState(isPending = true, isCompleted = true, isFailed = true)
```

**✅ DO (Sealed hierarchy restricting state space strictly to valid domain
states)**

```kotlin
sealed interface OrderState {
    data object Pending : OrderState
    data object Processing : OrderState
    data class Completed(val orderId: String, val timestamp: Long) : OrderState
    data class Failed(val reason: String, val code: Int) : OrderState
}
```
