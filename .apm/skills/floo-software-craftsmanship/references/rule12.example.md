# Rule 12: Prefer Scoped Declarations Over Top-Level State and Behavior — Reference Examples

Deep dive for [SKILL.md — Rule 12](../SKILL.md).

______________________________________________________________________

The principle is language-independent:

> **Top-level variables and functions are for intentionally general-purpose declarations. Everything else should have an explicit owner or scope.**

This is primarily about ownership and discoverability. When code belongs to a feature, type, domain concept, or implementation detail, put it there.

## Swift

### ❌ DON'T

```swift
let maxRetryCount = 3

func formatUserName(_ user: User) -> String {
    "\(user.firstName) \(user.lastName)"
}

func calculateOrderTotal(_ order: Order) -> Money {
    // ...
}
```

### ✅ DO

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

## Kotlin

### ❌ DON'T

```kotlin
const val MAX_RETRIES = 3

fun formatUserName(user: User): String =
    "${user.firstName} ${user.lastName}"

fun calculateOrderTotal(order: Order): Money =
    // ...
```

### ✅ DO

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

## JavaScript / TypeScript

### ❌ DON'T

```typescript
const maxRetries = 3;

function formatUserName(user: User): string {
    return `${user.firstName} ${user.lastName}`;
}

function calculateOrderTotal(order: Order): Money {
    // ...
}
```

### ✅ DO

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

## Java

### ❌ DON'T

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

### ✅ DO

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

## Python

### ❌ DON'T

```python
MAX_RETRIES = 3


def format_user_name(user: User) -> str:
    return f"{user.first_name} {user.last_name}"


def calculate_order_total(order: Order) -> Money: ...
```

### ✅ DO

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

## Summary

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
