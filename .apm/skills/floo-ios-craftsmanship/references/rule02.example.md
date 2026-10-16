# Rule 2: Stateless ViewModifiers Belong in Extension Helpers (SwiftUI) — Reference Examples

Deep dive for [SKILL.md — Rule 2](../SKILL.md).

______________________________________________________________________

## Stateful vs Stateless Modifiers in SwiftUI

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
