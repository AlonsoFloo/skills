# iOS Craftsmanship - Deep Dives & Reference Examples

This document provides extended examples and memory management guidelines for
[SKILL.md](../SKILL.md).

______________________________________________________________________

## Rule 1: Prevent Memory Leaks (`self` and `weak` references)

### Weak Capture Patterns in Combine and Async Closures

```swift
// Combine pipeline memory leak prevention
cancellable = publisher
    .sink { [weak self] completion in
        guard let self = self else { return }
        self.handleCompletion(completion)
    } receiveValue: { [weak self] value in
        guard let self = self else { return }
        self.handleValue(value)
    }
```

______________________________________________________________________

## Rule 2: Stateless ViewModifiers Belong in Extension Helpers (SwiftUI)

### Stateful vs Stateless Modifiers in SwiftUI

Stateful ViewModifiers are appropriate when managing view state or animations:

```swift
// Stateful ViewModifier IS warranted when holding state
struct ShakeEffect: ViewModifier {
    var animatableData: CGFloat
    func body(content: Content) -> some View {
        content.offset(x: sin(animatableData * .pi * 2) * 5)
    }
}
```
