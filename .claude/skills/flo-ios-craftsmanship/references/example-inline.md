# Inline Annotations — Reference Examples

Deep dive for [SKILL.md — Rule 3](../SKILL.md).

> SE-0496 (`@inline(always)`), SE-0497 (`@export`), SE-0193 (`@inlinable`),
> SE-0460 (`@specialized`).

______________________________________________________________________

## What Inlining Does

Compiler copies function body into caller. Call overhead gone. Optimizer sees
more context. **But** every call site gets its own copy → bigger binary.

______________________________________________________________________

## `@inline(always)` — Must Inline (Swift 6.3+)

Hard guarantee. Compiler inlines or errors. Not a hint.

**❌ DON'T**

```swift
// Non-final class method → compile error
class Processor {
    @inline(always) func run() {} // error: non-final method
}

// Recursion → compile error
@inline(always) func ping() { pong() } // error: inlining cycle
@inline(always) func pong() { ping() }

// Large body → binary bloat at every call site
@inline(always)
func buildDashboard() -> some View { /* 200+ lines */ }
```

**✅ DO**

```swift
// Small hot helper on value type
struct Vec3 {
    var x, y, z: Float
    @inline(always)
    func dot(_ o: Vec3) -> Float { x*o.x + y*o.y + z*o.z }
}

// Final class method — dynamic dispatch eliminated
class Renderer {
    @inline(always) final func clear() { /* … */ }
}

// Static method on class
class MathLib {
    @inline(always)
    static func lerp(_ a: Float, _ b: Float, t: Float) -> Float {
        a + (b - a) * t
    }
}
```

**Public = `@inlinable` implied.** Body exposed to clients. All `@inlinable`
rules apply.

**Protocol / first-class values = no guarantee, no error.**

```swift
let fn = myInlinedFunc  // stored as value
fn()                    // NOT guaranteed inlined
```

______________________________________________________________________

## `@inline(never)` — Must Not Inline (stable)

Cold paths stay out of line. Stack traces preserved.

**❌ DON'T**

```swift
// Hot-path helper — forces a function call on every iteration
@inline(never)
func square(_ x: Float) -> Float { x * x }

func render(_ values: [Float]) {
    for v in values {
        let s = square(v) // ← call overhead on every iteration
    }
}
```

**✅ DO**

```swift
// Cold error path — keeps the hot loop tight
@inline(never)
func reportError(_ msg: String) { logger.error(msg) }

func processItems(_ items: [Item]) {
    for item in items {
        guard item.isValid else {
            reportError("Invalid: \(item.id)") // cold, stays out of line
            continue
        }
        // hot path …
    }
}
```

______________________________________________________________________

## `@inlinable` — Expose Body Cross-Module (SE-0193)

Not a directive. Just makes body *visible* to other modules. Optimizer still
decides.

**❌ DON'T**

```swift
// Leaking internal type from @inlinable body
@inlinable
public func create() -> InternalModel { // ❌ error: InternalModel is internal
    InternalModel()
}
```

**✅ DO**

```swift
@inlinable
public func clamped<T: Comparable>(_ v: T, to r: ClosedRange<T>) -> T {
    min(max(v, r.lowerBound), r.upperBound)
}

// Internal helper needed by @inlinable — mark @usableFromInline
@usableFromInline
internal struct Bounds<T: Comparable> { /* … */ }
```

Body can only reference `public` / `@usableFromInline` symbols. Once shipped →
**ABI contract**.

______________________________________________________________________

## `@specialized` — Pre-Specialize Generics (SE-0460, Swift 6.3+)

Generic code behind existentials or binary frameworks can't be specialized by
the caller. `@specialized` tells the compiler to generate concrete versions
**inside** the function. At runtime it checks the type and redispatches.

**❌ DON'T**

```swift
// Missing generic placeholders — ALL must be specified
extension Dictionary where Value: BinaryInteger {
    @specialized(where Value == Int) // ❌ error: missing Key
    func sum() -> Double {
        values.reduce(0) { $0 + Double($1) }
    }
}

// Specializing types that will never appear at runtime — dead code
extension Sequence where Element: BinaryInteger {
    @specialized(where Self == [Float]) // ❌ Float is not BinaryInteger
    func sum() -> Double {
        reduce(0) { $0 + Double($1) }
    }
}

// Spraying @specialized on every generic — adds runtime type-check overhead
// Only specialize types you KNOW appear behind existentials or opaque calls
@specialized(where T == Int)
@specialized(where T == Int8)
@specialized(where T == Int16)
@specialized(where T == Int32)
@specialized(where T == Int64)
@specialized(where T == UInt)
@specialized(where T == UInt8)   // ❌ Too many — each adds a branch
func convert<T: BinaryInteger>(_ v: T) -> Double { Double(v) }
```

**✅ DO**

```swift
// Specialize for the 1–2 concrete types that actually appear behind existentials
extension Sequence where Element: BinaryInteger {
    @specialized(where Self == [Int])
    @specialized(where Self == [Int8])
    func sum() -> Double {
        reduce(0) { $0 + Double($1) }
    }
}

// All placeholders fully specified on Dictionary
extension Dictionary where Value: BinaryInteger {
    @specialized(where Key == String, Value == Int)
    func sumValues() -> Double {
        values.reduce(0) { $0 + Double($1) }
    }
}

// Works on computed properties (must go on `get` explicitly)
extension Array where Element: BinaryInteger {
    var total: Double {
        @specialized(where Element == Int)
        get { reduce(0) { $0 + Double($1) } }
    }
}
```

**Key points:**

- Exact type match only — no subclass, no implicit conversion.
- No ABI impact — specializations are internal dispatch.
- Profile first — each `@specialized` adds a type-check branch at entry.

______________________________________________________________________

## Pitfalls

1. **Spraying `@inline(always)` everywhere** — binary bloat, icache thrashing.
   Profile first.
1. **Large bodies** — legal but every call site gets the full copy.
1. **Non-final class methods** — compile error. Mark `final` or use struct.
1. **`@inlinable` leaking internals** — body can't reference `internal` types
   unless `@usableFromInline`.
1. **Stale annotations** — small function grows after refactor. Review during PR.
1. **First-class values** — `let fn = f; fn()` silently bypasses the guarantee.
1. **Over-specializing generics** — each `@specialized` adds a runtime branch.
   Only specialize types you measured behind existentials.
