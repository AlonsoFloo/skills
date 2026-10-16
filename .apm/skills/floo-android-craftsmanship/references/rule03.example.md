# Rule 3: Stateless ViewModifiers Belong in Extension Helpers (Compose) — Reference Examples

Deep dive for [SKILL.md — Rule 3](../SKILL.md).

______________________________________________________________________

## Stateless vs Stateful Modifiers in Compose

A plain `Modifier` extension is enough when the chain only combines other
modifiers. Marking it `@Composable` forces it to run inside composition and
prevents it from being used outside a composable scope.

```kotlin
// Stateless chain: plain extension function
fun Modifier.cardStyle(): Modifier = this
    .padding(16.dp)
    .clip(RoundedCornerShape(12.dp))
    .background(Color.LightGray.copy(alpha = 0.1f))
```

When a modifier needs its own state or animation, use `Modifier.Node` (or
`composed {}` in legacy code) instead of a `@Composable` function:

```kotlin
// Stateful modifier: holds internal state, so a Modifier.Node is warranted
fun Modifier.shake(trigger: Boolean): Modifier = this then ShakeElement(trigger)

private data class ShakeElement(val trigger: Boolean) : ModifierNodeElement<ShakeNode>() {
    override fun create() = ShakeNode(trigger)
    override fun update(node: ShakeNode) { node.trigger = trigger }
}

private class ShakeNode(var trigger: Boolean) : Modifier.Node() { /* animate offset */ }
```
