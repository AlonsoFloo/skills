---
name: core-engineering
description: Core architecture & design principles
---

# Core Engineering

> Core architecture & design principles

This skill is derived from the **Core Engineering Skills** specification supplied with this repository.
Treat the rules below as engineering guidance for AI coding assistants, code reviewers, and engineers.
When a rule is context-dependent, prefer explicit domain reasoning over mechanical application.

## Rule 01: Avoid Booleans for Complex State or Results

**Description:** Representing multi-faceted business logic or workflow states using combinations of `Boolean` flags creates invalid state space (e.g. `isLoading = true` and `isSuccess = true`). Always model complex domain states using explicit `enum class`, `sealed class`, or `Result` types.

**❌ DON'T**

```kotlin
// Multi-flag nightmare allowing impossible state combinations
data class PageState(
    var isLoading: Boolean = false,
    var isSuccess: Boolean = false,
    var isError: Boolean = false,
)
```

**✅ DO**

```kotlin
// Type-safe domain representation
sealed class PageState {
    data object Idle : PageState
    data object Loading : PageState
    data class Success(val data: Any) : PageState
    data class Error(val message: String) : PageState
}
```

## Rule 02: Prefer Strategy Pattern Over Unbounded Switches

**Description:** If you cannot predict or bound the number of branches in a `switch` or `when` statement before implementation, avoid giant conditional trees. Instead, encapsulate variations using the **Strategy Pattern** or polymorphism to maintain Open/Closed Principle adherence.

**❌ DON'T**

```kotlin
// Ever-growing conditional statement requiring edit on every new variant
fun processPayment(type: String, amount: Double) {
    when (type) {
        "CREDIT" -> payWithCredit(amount)
        "PAYPAL" -> payWithPaypal(amount)
        "CRYPTO" -> payWithCrypto(amount)
        "APPLE_PAY" -> payWithApplePay(amount)
        // Constantly modified whenever new payment methods are added
    }
}
```

**✅ DO**

```kotlin
interface PaymentStrategy {
    fun process(amount: Double)
}

class PaymentProcessor(private val strategies: Map<PaymentType, PaymentStrategy>) {
    fun processPayment(type: PaymentType, amount: Double) {
        strategies[type]?.process(amount) ?: throw IllegalArgumentException("Unsupported type")
    }
}
```

## Rule 03: Enforce Immutability Post-Construction

**Description:** Once an object has finished its construction phase, its state must remain strictly immutable. Continuous post-construction mutation invites thread-safety risks, subtle side effects, and unpredictable behavior across application layers. Encapsulate mutable state logic inside a dedicated **Builder** or temporary builder block.

**❌ DON'T**

```kotlin
// Object remains mutable post-construction—allowing external callers or threads to mutate state anytime
class NetworkRequest {
    var url: String = ""
    var timeoutMs: Long = 5000
    val headers: MutableMap<String, String> = mutableMapOf() // Exposed mutable collection!

    fun addHeader(key: String, value: String) {
        headers[key] = value // Post-construction state mutation!
    }
}
```

**✅ DO**

```kotlin
// Encapsulate mutation to the Builder phase; output instance is 100% immutable post-construction
class NetworkRequest private constructor(
    val url: String,
    val timeoutMs: Long,
    val headers: Map<String, String>, // Read-only immutable map snapshot
) {
    class Builder {
        var url: String = ""
        var timeoutMs: Long = 5000
        private val headers = mutableMapOf<String, String>()

        fun header(key: String, value: String) = apply { headers[key] = value }

        fun build(): NetworkRequest {
            require(url.isNotBlank()) { "URL cannot be blank" }
            return NetworkRequest(url, timeoutMs, headers.toMap()) // Freeze state upon construction
        }
    }
}
```

## Rule 04: Amdahl's Law

**Description:** No matter how many CPU cores, background threads, or parallel workers you throw at a task, the maximum overall speedup you can achieve is strictly capped by the parts of the program that **must run sequentially** (such as database locks, file I/O, global state synchronization, or main-thread UI rendering). Do not waste engineering effort parallelizing parts of the system that are already fast without addressing the sequential bottleneck.

## Rule 05: Law of Demeter (Principle of Least Knowledge)

**Description:** A method of an object should only invoke methods of:

1. Itself
1. Its parameters
1. Objects it creates/instantiates
1. Its direct component sub-objects

Avoid reaching through nested object chains (e.g. `a.getB().getC().getD().doSomething()`).

## Rule 06: SOLID Principles

**Description:** Enforce the 5 fundamental object-oriented software architecture principles:

- **S** - Single Responsibility: A class should have one, and only one, reason to change.
- **O** - Open/Closed: Software entities should be open for extension, but closed for modification.
- **L** - Liskov Substitution: Subtypes must be substitutable for their base types without altering correctness.
- **I** - Interface Segregation: Clients should not be forced to depend upon interfaces they do not use.
- **D** - Dependency Inversion: Depend upon abstractions, not concretions.

______________________________________________________________________

## ⚙️ DOMAIN: CODE LOGIC & PATTERNS
