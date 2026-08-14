---
name: ios-swift
description: iOS & Swift engineering
---

# Ios Swift

> iOS & Swift engineering

This skill is derived from the **Core Engineering Skills** specification supplied with this repository.
Treat the rules below as engineering guidance for AI coding assistants, code reviewers, and engineers.
When a rule is context-dependent, prefer explicit domain reasoning over mechanical application.

## Rule 19: Prevent Memory Leaks (`self` and `weak` references)

**Description:** In closure-heavy or reference-cycling environments, always ensure long-lived references don't capture `self` strongly. Always consider retain cycles and specify weak/unowned capture.

**❌ DON'T**

```swift
// Swift strong reference cycle
networkManager.fetchData { result in
    self.updateUI(with: result) // Holds strong reference to self!
}
```

**✅ DO**

```swift
// Swift weak self capture pattern
networkManager.fetchData { [weak self] result in
    guard let self = self else { return }
    self.updateUI(with: result)
}
```

## Rule 20: Stateless ViewModifiers Belong in Extension Helpers (SwiftUI)

**Description:** Creating a custom `struct ViewModifier` in SwiftUI for a purely stateless style chain is unnecessary. Plain stateless modifier chains should be declared as standard extension functions on `View`.

**❌ DON'T**

```swift
// SwiftUI: Creating a custom ViewModifier struct for a purely stateless style chain
struct CardStyleModifier: ViewModifier {
    func body(content: Content) -> some View {
        content.padding(16).background(Color.gray.opacity(0.1)).cornerRadius(12)
    }
}
```

**✅ DO**

```swift
// SwiftUI: Stateless modifiers belong in plain View extension helpers
extension View {
    func cardStyle() -> some View {
        self.padding(16).background(Color.gray.opacity(0.1)).cornerRadius(12)
    }
}
```

______________________________________________________________________

## 🚀 DOMAIN: DEVOPS & TOOLING
