---
name: flo-software-craftsmanship
description: Software architecture, domain modeling, code logic & engineering patterns
---

# Software Craftsmanship

> Core architecture, domain modeling, pragmatic code logic & design principles

Treat the rules below as engineering guidance for AI coding assistants, code
reviewers, and software engineers. When a rule is context-dependent, prefer
explicit domain reasoning over mechanical application. For comprehensive
examples and deep dives, see [references/examples.md](references/examples.md).

## Rule 1: Avoid Booleans for Complex State or Results

**Description:** Representing multi-faceted business logic or workflow states
using combinations of boolean flags creates invalid state space (e.g.
`isLoading = true` and `isSuccess = true`). Always model complex domain states
using explicit enum, sealed class, or union types.

**❌ DON'T**

```kotlin
data class PageState(
    var isLoading: Boolean = false,
    var isSuccess: Boolean = false,
    var isError: Boolean = false,
)
```

**✅ DO**

```kotlin
sealed class PageState {
    data object Idle : PageState
    data object Loading : PageState
    data class Success(val data: Any) : PageState
    data class Error(val message: String) : PageState
}
```

*See
[references/examples.md#rule-1-avoid-booleans-for-complex-state-or-results](references/examples.md#rule-1-avoid-booleans-for-complex-state-or-results)
for detailed reference cases.*

______________________________________________________________________

## Rule 2: Prefer Strategy Pattern Over Unbounded Switches

**Description:** If you cannot predict or bound the number of branches in a
conditional before implementation, avoid giant `switch` or `when` trees.
Encapsulate variations using the Strategy Pattern or polymorphism to preserve
the Open/Closed Principle.

**❌ DON'T**

```kotlin
fun processPayment(type: String, amount: Double) {
    when (type) {
        "CREDIT" -> payWithCredit(amount)
        "PAYPAL" -> payWithPaypal(amount)
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

*See
[references/examples.md#rule-2-prefer-strategy-pattern-over-unbounded-switches](references/examples.md#rule-2-prefer-strategy-pattern-over-unbounded-switches)
for detailed reference cases.*

______________________________________________________________________

## Rule 3: Enforce Immutability Post-Construction

**Description:** Once an object finishes construction, its state must remain
strictly immutable. Continuous post-construction mutation invites thread-safety
risks and subtle side effects. Encapsulate mutable state inside a dedicated
Builder or temporary construction block.

**❌ DON'T**

```kotlin
class NetworkRequest {
    var url: String = ""
    val headers: MutableMap<String, String> = mutableMapOf()
}
```

**✅ DO**

```kotlin
class NetworkRequest private constructor(
    val url: String,
    val headers: Map<String, String>,
) {
    class Builder {
        var url: String = ""
        private val headers = mutableMapOf<String, String>()
        fun header(key: String, value: String) = apply { headers[key] = value }
        fun build() = NetworkRequest(url, headers.toMap())
    }
}
```

*See
[references/examples.md#rule-3-enforce-immutability-post-construction](references/examples.md#rule-3-enforce-immutability-post-construction)
for detailed reference cases.*

______________________________________________________________________

## Rule 4: Amdahl's Law

**Description:** The maximum overall speedup achieved by parallelizing a system
is strictly capped by the parts that must run sequentially (e.g., locks, file
I/O, UI rendering). Focus optimization efforts on sequential bottlenecks rather
than parallelizing already fast components.

**❌ DON'T**

```kotlin
// Spawning 32 threads to process items while all threads wait on a single synchronous DB lock
```

**✅ DO**

```kotlin
// Eliminate the DB lock bottleneck first before scaling parallel workers
```

*See
[references/examples.md#rule-4-amdahls-law](references/examples.md#rule-4-amdahls-law)
for detailed reference cases.*

______________________________________________________________________

## Rule 5: Law of Demeter (Principle of Least Knowledge)

**Description:** A method should only invoke methods of itself, its parameters,
objects it instantiates, or its direct component sub-objects. Avoid reaching
through nested object chains (e.g. `a.getB().getC().getD().doSomething()`).

**❌ DON'T**

```kotlin
val zipCode = user.getAccount().getAddress().getZipCode()
```

**✅ DO**

```kotlin
val zipCode = user.getZipCode()
```

*See
[references/examples.md#rule-5-law-of-demeter-principle-of-least-knowledge](references/examples.md#rule-5-law-of-demeter-principle-of-least-knowledge)
for detailed reference cases.*

______________________________________________________________________

## Rule 6: SOLID Principles

**Description:** Enforce the 5 core software architecture principles: Single
Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and
Dependency Inversion.

**❌ DON'T**

```kotlin
class UserManager {
    fun saveUser(user: User) { /* DB logic */ }
    fun sendEmail(user: User) { /* SMTP logic */ }
}
```

**✅ DO**

```kotlin
class UserRepository { fun save(user: User) { ... } }
class EmailNotifier { fun sendWelcome(user: User) { ... } }
```

*See
[references/examples.md#rule-6-solid-principles](references/examples.md#rule-6-solid-principles)
for detailed reference cases.*

______________________________________________________________________

## Rule 7: Parse, Don't Validate

**Description:** Validation checks a predicate without refining the underlying
data type, forcing downstream functions to re-check validity. Parsing transforms
an unrefined type into a refined domain type at system boundaries, serving as a
static proof of validity.

**❌ DON'T**

```kotlin
fun validateNotEmpty(list: List<String>) {
    if (list.isEmpty()) throw IllegalArgumentException()
}
```

**✅ DO**

```kotlin
data class NonEmptyList<out T>(val head: T, val tail: List<T>) {
    companion object {
        fun <T> parse(list: List<T>): Result<NonEmptyList<T>> =
            if (list.isNotEmpty()) Result.success(NonEmptyList(list.first(), list.drop(1)))
            else Result.failure(IllegalArgumentException())
    }
}
```

*See
[references/examples.md#rule-7-parse-dont-validate](references/examples.md#rule-7-parse-dont-validate)
for detailed reference cases.*

______________________________________________________________________

## Rule 8: Postel's Law (Robustness Principle)

**Description:** Be conservative in what you produce (output strictly validated,
spec-compliant data) and liberal in what you accept (parse inputs forgivingly,
ignoring unknown fields with sensible defaults).

**❌ DON'T**

```kotlin
@Serializable
data class UserResponse(val id: String, val username: String) // Crashes on unexpected backend fields
```

**✅ DO**

```kotlin
val jsonConfig = Json { ignoreUnknownKeys = true }
@Serializable
data class UserResponse(val id: String, val username: String, val email: String = "")
```

*See
[references/examples.md#rule-8-postels-law-robustness-principle](references/examples.md#rule-8-postels-law-robustness-principle)
for detailed reference cases.*

______________________________________________________________________

## Rule 9: Isolate Quirky Workarounds in Dedicated Spaces

**Description:** When a hack or vendor workaround is strictly unavoidable,
isolate it in a dedicated file and document the exact reason, bug ticket
reference, and condition for removal.

**❌ DON'T**

```css
.btn-primary { margin-top: -3px; }
```

**✅ DO**

```css
/* Located in shame.css */
/* WORKAROUND (Safari 17 flexbox bug #12948): Offset button alignment */
.safari-legacy .btn-primary { margin-top: -3px; }
```

*See
[references/examples.md#rule-9-isolate-quirky-workarounds-in-dedicated-spaces](references/examples.md#rule-9-isolate-quirky-workarounds-in-dedicated-spaces)
for detailed reference cases.*

______________________________________________________________________

## Rule 10: Knuth's Optimization Principle

**Description:** Premature optimization is the root of all evil. Write clean,
readable, correct code first. Do not add complex caching or manual memory hacks
until empirical benchmarks prove a bottleneck exists.

**❌ DON'T**

```kotlin
// Writing custom bitwise cache before establishing performance requirements
```

**✅ DO**

```kotlin
// Implement straightforward logic, profile with trace tools, optimize bottlenecks
```

*See
[references/examples.md#rule-10-knuths-optimization-principle](references/examples.md#rule-10-knuths-optimization-principle)
for detailed reference cases.*

______________________________________________________________________

## Rule 11: YAGNI (You Aren't Gonna Need It)

**Description:** Implement features only when you actually need them, never when
you merely foresee that you might need them. Unused abstraction layers add
complexity and surface area for bugs.

**❌ DON'T**

```kotlin
interface GenericDataStoreRepositoryFactoryProvider { ... } // Built "just in case"
```

**✅ DO**

```kotlin
class SimpleUserRepository(private val api: UserApi) { ... }
```

*See
[references/examples.md#rule-11-yagni-you-arent-gonna-need-it](references/examples.md#rule-11-yagni-you-arent-gonna-need-it)
for detailed reference cases.*

______________________________________________________________________

## Rule 12: Prefer Scoped Declarations Over Top-Level State and Behavior

**Description:** Do not place variables or functions at the top level by default.

Top-level declarations are reserved for things that are explicitly intended to be general, shared, and independent of a specific feature, type, or domain object.

All feature-specific or contextual code must have an explicit owner or scope: an extension, private helper, feature/module scope, data type, object, class, namespace, or another cohesive abstraction.

The goal is not to ban top-level declarations. The goal is to make scope intentional: if a variable or function belongs to a concept, that concept should own it.

**❌ DON'T**

```kotlin
val maxRetries = 3

fun formatUserName(user: User): String =
    "${user.firstName} ${user.lastName}"

fun calculateOrderTotal(order: Order): Money =
    /* ... */
```

**✅ DO**

```kotlin
private const val MAX_RETRIES = 3

fun User.formatName(): String =
    "$firstName $lastName"

class OrderTotals {
    fun calculate(order: Order): Money =
        /* ... */
}
```

Use the most natural scope for the language and domain:

- `private` for implementation details
- extensions for behavior that naturally belongs to an existing type
- feature/module scopes for feature-specific behavior
- data/value types when state and behavior form a cohesive concept
- objects/namespaces when a concept requires a named singleton or namespace
- top-level declarations only when the declaration is intentionally general

A top-level declaration should therefore be treated as an explicit architectural decision, not the default place to put code.

*See [references/examples.md#rule-12-prefer-scoped-declarations-over-top-level-state-and-behavior](references/examples.md#rule-12-prefer-scoped-declarations-over-top-level-state-and-behavior) for examples in Swift, Kotlin, JavaScript, Java, and Python.*
