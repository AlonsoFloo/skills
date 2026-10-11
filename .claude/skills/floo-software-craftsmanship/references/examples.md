# Software Craftsmanship - Deep Dives & Reference Examples

This document provides extended examples, architectural deep dives, and
additional edge cases for the rules defined in [SKILL.md](../SKILL.md).

______________________________________________________________________

## Rule 1: Avoid Booleans for Complex State or Results

### Architectural Deep Dive

When representing UI state or multi-step workflow outcomes, using independent
Boolean properties creates invalid state combinations ($2^N$ states for $N$
boolean variables).

#### Comprehensive Example

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

______________________________________________________________________

## Rule 2: Prefer Strategy Pattern Over Unbounded Switches

### Architectural Deep Dive

When business requirements introduce new variants (e.g. new payment providers,
export formats, notification channels), editing an central `switch`/`when` block
violates the Open/Closed Principle and risks breaking existing handlers.

#### Comprehensive Example

**❌ DON'T (Unbounded conditional branching)**

```kotlin
class ReportExporter {
    fun export(data: ReportData, format: String): ByteArray {
        return when (format) {
            "PDF" -> generatePdf(data)
            "CSV" -> generateCsv(data)
            "EXCEL" -> generateExcel(data)
            "JSON" -> generateJson(data)
            else -> throw IllegalArgumentException("Unsupported format: $format")
        }
    }
}
```

**✅ DO (Polymorphic Strategy Pattern with registry lookup)**

```kotlin
interface ExportStrategy {
    val format: ExportFormat
    fun export(data: ReportData): ByteArray
}

class PdfExportStrategy : ExportStrategy {
    override val format = ExportFormat.PDF
    override fun export(data: ReportData): ByteArray = /* PDF generation */
}

class ReportExportService(strategies: List<ExportStrategy>) {
    private val strategyMap = strategies.associateBy { it.format }

    fun export(data: ReportData, format: ExportFormat): ByteArray {
        val strategy = strategyMap[format]
            ?: throw IllegalArgumentException("No exporter registered for $format")
        return strategy.export(data)
    }
}
```

______________________________________________________________________

## Rule 3: Enforce Immutability Post-Construction

### Architectural Deep Dive

Mutable domain objects or exposed mutable state enable unexpected mutations
across thread boundaries or layer boundaries. Post-construction state freezing
ensures predictable behavior.

#### Comprehensive Example

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

______________________________________________________________________

## Rule 4: Amdahl's Law

### Architectural Deep Dive

Amdahl's law demonstrates that $S\_{\\text{latency}}(s) = \\frac{1}{(1 - p) +
\\frac{p}{s}}$, where $p$ is the proportion of parallelizable execution and $s$
is the speedup factor. Sequential bottlenecks quickly limit performance gains.

#### Comprehensive Example

**❌ DON'T (Optimizing parallel worker count while synchronous file I/O blocks
queue)**

```kotlin
// Launching 64 coroutines to write log lines to a single non-concurrent FileOutputStream
val executor = Executors.newFixedThreadPool(64)
repeat(10_000) { id ->
    executor.submit {
        synchronized(fileLock) {
            fileStream.write("Log $id\n".toByteArray())
        }
    }
}
```

**✅ DO (Eliminate sequential bottlenecks using async channels or batching
first)**

```kotlin
// Channel converts lock contention into sequential batch writes on single background worker
val logChannel = Channel<String>(capacity = 10_000)
// Single writer coroutine processes non-blocking batch writes
```

______________________________________________________________________

## Rule 5: Law of Demeter (Principle of Least Knowledge)

### Architectural Deep Dive

Reaching into deeply nested object graphs tightly couples the caller to the
entire path structure. Refactor callers to interact directly with immediate
dependencies.

#### Comprehensive Example

**❌ DON'T (Deep coupling across structural boundaries)**

```kotlin
fun printCustomerZip(order: Order) {
    val zip = order.getInvoice().getRecipient().getAddress().getZipCode()
    println(zip)
}
```

**✅ DO (Delegation method encapsulating path navigation)**

```kotlin
class Order(private val invoice: Invoice) {
    fun getDeliveryZipCode(): String = invoice.recipientZipCode
}

fun printCustomerZip(order: Order) {
    println(order.getDeliveryZipCode())
}
```

______________________________________________________________________

## Rule 6: SOLID Principles

### Architectural Deep Dive

- **Single Responsibility Principle (SRP):** High cohesion by separating
  database, business, and serialization logic.
- **Open/Closed Principle (OCP):** Open for extension via interfaces, closed for
  modification.
- **Liskov Substitution Principle (LSP):** Subclasses fulfill parent contracts
  without unexpected exceptions.
- **Interface Segregation Principle (ISP):** Small, client-focused interfaces
  rather than god interfaces.
- **Dependency Inversion Principle (DIP):** Modules depend on abstractions,
  high-level code decoupled from concrete details.

______________________________________________________________________

## Rule 7: Parse, Don't Validate

### Architectural Deep Dive

Shotgun parsing happens when input checks are scattered across functions,
requiring repeated assertions. Formal parsing converts input into strongly typed
domain objects once at boundary entry points.

#### Comprehensive Example

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

______________________________________________________________________

## Rule 8: Postel's Law (Robustness Principle)

### Architectural Deep Dive

When receiving payloads over networks or inter-process communication, ensure
unexpected payload additions do not cause client failure, while outgoing
payloads rigidly adhere to schema specifications.

______________________________________________________________________

## Rule 9: Isolate Quirky Workarounds in Dedicated Spaces

### Architectural Deep Dive

Scattering hacks across feature modules masks technical debt. Isolate
workarounds in dedicated workaround files with ticket links, expiration dates,
and target platform versions.

______________________________________________________________________

## Rule 10: Knuth's Optimization Principle

### Architectural Deep Dive

Optimize only after measurement. Premature optimization introduces convoluted
abstractions, obfuscates intent, and increases bug probability without
measurable user benefit.

______________________________________________________________________

## Rule 11: YAGNI (You Aren't Gonna Need It)

### Architectural Deep Dive

Do not build extra layers, unused generic abstractions, or speculative extension
points. Implement precisely what is required now, keeping code straightforward
to refactor when future requirements arrive.

______________________________________________________________________

## Rule 12: Prefer Scoped Declarations Over Top-Level State and Behavior

The principle is language-independent:

> **Top-level variables and functions are for intentionally general-purpose declarations. Everything else should have an explicit owner or scope.**

This is primarily about ownership and discoverability. When code belongs to a feature, type, domain concept, or implementation detail, put it there.

### Swift

#### ❌ DON'T

```swift
let maxRetryCount = 3

func formatUserName(_ user: User) -> String {
    "\(user.firstName) \(user.lastName)"
}

func calculateOrderTotal(_ order: Order) -> Money {
    // ...
}
```

#### ✅ DO

```swift
private let maxRetryCount = 3

extension User {
    func formattedName() -> String {
        "\(firstName) \(lastName)"
    }
}

struct OrderTotals {
    func calculate(_ order: Order) -> Money {
        // ...
    }
}
```

### Kotlin

#### ❌ DON'T

```kotlin
const val MAX_RETRIES = 3

fun formatUserName(user: User): String =
    "${user.firstName} ${user.lastName}"

fun calculateOrderTotal(order: Order): Money =
    // ...
```

#### ✅ DO

```kotlin
private const val MAX_RETRIES = 3

fun User.formatName(): String =
    "$firstName $lastName"

class OrderTotals {
    fun calculate(order: Order): Money =
        // ...
}
```

For feature-specific state, use an explicit owner, or companion object:

```kotlin
object Checkout {
    private const val MAX_RETRIES = 3

    fun execute(order: Order) {
        // ...
    }
}
```

### JavaScript / TypeScript

#### ❌ DON'T

```typescript
const maxRetries = 3;

function formatUserName(user: User): string {
    return `${user.firstName} ${user.lastName}`;
}

function calculateOrderTotal(order: Order): Money {
    // ...
}
```

#### ✅ DO

```typescript
const checkout = {
    maxRetries: 3,

    execute(order: Order): void {
        // ...
    },
};

class UserFormatter {
    format(user: User): string {
        return `${user.firstName} ${user.lastName}`;
    }
}
```

A genuinely general-purpose function can remain top-level:

```typescript
export function clamp(value: number, min: number, max: number): number {
    return Math.min(Math.max(value, min), max);
}
```

### Java

#### ❌ DON'T

```java
public static final int MAX_RETRIES = 3;

public static String formatUserName(User user) {
    return user.firstName() + " " + user.lastName();
}

public static Money calculateOrderTotal(Order order) {
    // ...
}
```

Do not use miscellaneous `Utils` classes as a dumping ground:

```java
public final class UserUtils {

    public static String formatUserName(User user) {
        // ...
    }

    public static Money calculateOrderTotal(Order order) {
        // ...
    }
}
```

#### ✅ DO

```java
public final class User {

    public String formattedName() {
        return firstName + " " + lastName;
    }
}
```

```java
public final class OrderTotals {

    public Money calculate(Order order) {
        // ...
    }
}
```

General-purpose functionality can intentionally remain in a utility type:

```java
public final class MathUtils {

    private MathUtils() {}

    public static int clamp(int value, int min, int max) {
        return Math.min(Math.max(value, min), max);
    }
}
```

### Python

#### ❌ DON'T

```python
MAX_RETRIES = 3


def format_user_name(user: User) -> str:
    return f"{user.first_name} {user.last_name}"


def calculate_order_total(order: Order) -> Money: ...
```

#### ✅ DO

```python
class Checkout:
    MAX_RETRIES = 3

    def execute(self, order: Order) -> None: ...
```

Behavior belonging to a type should live on that type:

```python
@dataclass
class User:
    first_name: str
    last_name: str

    def formatted_name(self) -> str:
        return f"{self.first_name} {self.last_name}"
```

Feature-specific helpers can remain module-private:

```python
def _calculate_order_total(order: Order) -> Money: ...
```

A genuinely reusable function can remain at module level:

```python
def clamp(value: int, minimum: int, maximum: int) -> int:
    return min(max(value, minimum), maximum)
```

### Summary

| Language              | Preferred scope                                               |
| --------------------- | ------------------------------------------------------------- |
| Swift                 | `private`, extensions, structs/classes, nested types          |
| Kotlin                | `private`, extensions, classes, objects                       |
| JavaScript/TypeScript | feature modules, classes/objects, module-private declarations |
| Java                  | classes, packages, private members, explicit utility types    |
| Python                | classes, module-private (`_`) declarations, modules           |

The rule is **not**:

> "Never use top-level functions or variables."

The rule is:

> **"Don't use top-level scope unless the declaration is intentionally general-purpose."**
