---
name: flo-ios-craftsmanship
description: iOS architecture, Swift memory management & SwiftUI best practices
license: Complete terms in LICENSE
metadata:
  author: github.com/AlonsoFloo
  keywords:
    - AlonsoFloo
    - flo
    - ios
    - swift
    - swiftui
    - memory-management
    - viewmodifier
    - architecture
    - performance
---

# iOS Craftsmanship

> Modern iOS architecture, Swift memory safety & SwiftUI craftsmanship

Treat the rules below as engineering guidance for AI coding assistants, code
reviewers, and software engineers. For comprehensive examples and deep dives,
see [references/examples.md](references/examples.md).

## Rule 1: Prevent Memory Leaks (`self` and `weak` references)

**Description:** In closure-heavy or reference-cycling environments, ensure
long-lived references do not capture `self` strongly to prevent memory leaks and
retain cycles.

**❌ DON'T**

```swift
networkManager.fetchData { result in
    self.updateUI(with: result) // Retain cycle on self
}
```

**✅ DO**

```swift
networkManager.fetchData { [weak self] result in
    guard let self = self else { return }
    self.updateUI(with: result)
}
```

*See
[references/examples.md#rule-1-prevent-memory-leaks-self-and-weak-references](references/examples.md#rule-1-prevent-memory-leaks-self-and-weak-references)
for detailed reference cases.*

______________________________________________________________________

## Rule 2: Stateless ViewModifiers Belong in Extension Helpers (SwiftUI)

**Description:** Creating a custom `struct ViewModifier` in SwiftUI for a purely
stateless style chain is unnecessary. Plain stateless modifier chains should be
declared as standard extension functions on `View`.

**❌ DON'T**

```swift
struct CardStyleModifier: ViewModifier {
    func body(content: Content) -> some View {
        content.padding(16).background(Color.gray.opacity(0.1)).cornerRadius(12)
    }
}
```

**✅ DO**

```swift
extension View {
    func cardStyle() -> some View {
        self.padding(16).background(Color.gray.opacity(0.1)).cornerRadius(12)
    }
}
```

*See
[references/examples.md#rule-2-stateless-viewmodifiers-belong-in-extension-helpers-swiftui](references/examples.md#rule-2-stateless-viewmodifiers-belong-in-extension-helpers-swiftui)
for detailed reference cases.*

______________________________________________________________________

## Rule 3: Use Optimizer Annotations Deliberately (`@inline`, `@inlinable`, `@specialized`)

**Description:** Swift provides several attributes to control function inlining
and definition visibility. Each serves a distinct purpose — using the wrong one
causes compile errors, ABI leaks, or silent performance regressions. Choose
the minimal annotation that satisfies your actual need.

### Quick decision guide

| You want to…                                                               | Use                                                    |
| -------------------------------------------------------------------------- | ------------------------------------------------------ |
| **Force** inlining at every direct call site (hot path, small fn)          | `@inline(always)` (SE-0496, Swift 6.3+)                |
| **Prevent** inlining entirely (cold path, debuggability)                   | `@inline(never)`                                       |
| **Expose** a `public` body for cross-module optimization                   | `@inlinable` (SE-0193)                                 |
| **Expose** body **without** emitting an ABI symbol                         | `@export(implementation)` (SE-0497, Swift 6.3+)        |
| **Emit** an ABI symbol **without** exposing body                           | `@export(interface)` (SE-0497, Swift 6.3+)             |
| **Pre-specialize** a generic for concrete types (existentials, frameworks) | `@specialized` (SE-0460, Swift 6.3+)                   |
| Let the compiler decide                                                    | *Don't annotate* — the optimizer's heuristics are good |

**Checklist**

1. Small function? (≤ ~10 lines)
1. Hot path? (Instruments proof)
1. Optimizer fails on its own? (`swiftc -emit-sil -O`)
1. Value type or `final`?
1. If `public`: OK to expose body as ABI?

**Any "no" → don't annotate.**

### Common pitfalls

**❌ DON'T**

```swift
// 1. @inline(always) on non-final class method → compile error
class Processor {
    @inline(always) func run() { /* … */ } // ❌ error
}

// 2. @inline(always) on a large function → code-size bloat
@inline(always) func buildEntireUI() -> some View { /* 200+ lines */ }

// 3. Recursive @inline(always) → compile error (inlining cycle)
@inline(always) func ping() { pong() } // ❌
@inline(always) func pong() { ping() } // ❌

// 4. @inlinable leaking internal types in a library
@inlinable public func fetch() -> InternalModel { … } // ❌ error

// 5. @specialized without fully specifying all generic placeholders
@specialized(where Value == Int) // ❌ error: missing Key
func sum() -> Double { … } // on Dictionary<Key, Value>
```

**✅ DO**

```swift
// Small, measurably hot helper on a value type
struct Vector3 {
    var x, y, z: Float

    @inline(always)
    func dot(_ other: Vector3) -> Float {
        x * other.x + y * other.y + z * other.z
    }
}

// Cold error-handling path kept out of the hot loop
@inline(never)
func reportError(_ msg: String) { logger.error(msg) }

// Cross-module library utility — body exposed, ABI symbol emitted
@inlinable
public func clamp<T: Comparable>(_ v: T, _ lo: T, _ hi: T) -> T {
    min(max(v, lo), hi)
}

// Pre-specialize generic for known hot types behind existentials
extension Sequence where Element: BinaryInteger {
    @specialized(where Self == [Int])
    @specialized(where Self == [Int8])
    func sum() -> Double {
        reduce(0) { $0 + Double($1) }
    }
}
```

*See
[references/example-inline.md](references/example-inline.md)
for the full deep dive: all attributes, interaction matrix, ABI implications,
and when **not** to annotate.*
