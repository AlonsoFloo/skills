# Rule 1: Prevent Memory Leaks (`self` and `weak` references) — Reference Examples

Deep dive for [SKILL.md — Rule 1](../SKILL.md).

______________________________________________________________________

## Weak Capture Patterns in Combine and Async Closures

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
