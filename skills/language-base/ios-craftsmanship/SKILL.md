---
name: ios-craftsmanship
description: iOS architecture, Swift memory management & SwiftUI best practices
---

# iOS Craftsmanship

> Modern iOS architecture, Swift memory safety & SwiftUI craftsmanship

Treat the rules below as engineering guidance for AI coding assistants, code reviewers, and software engineers.
For comprehensive examples and deep dives, see [references/examples.md](references/examples.md).

## Rule 1: Prevent Memory Leaks (`self` and `weak` references)

**Description:** In closure-heavy or reference-cycling environments, ensure long-lived references do not capture `self` strongly to prevent memory leaks and retain cycles.

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

*See [references/examples.md#rule-1-prevent-memory-leaks-self-and-weak-references](references/examples.md#rule-1-prevent-memory-leaks-self-and-weak-references) for detailed reference cases.*

---

## Rule 2: Stateless ViewModifiers Belong in Extension Helpers (SwiftUI)

**Description:** Creating a custom `struct ViewModifier` in SwiftUI for a purely stateless style chain is unnecessary. Plain stateless modifier chains should be declared as standard extension functions on `View`.

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

*See [references/examples.md#rule-2-stateless-viewmodifiers-belong-in-extension-helpers-swiftui](references/examples.md#rule-2-stateless-viewmodifiers-belong-in-extension-helpers-swiftui) for detailed reference cases.*
