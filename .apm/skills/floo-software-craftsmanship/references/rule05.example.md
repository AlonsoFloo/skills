# Rule 5: Law of Demeter (Principle of Least Knowledge) — Reference Examples

Deep dive for [SKILL.md — Rule 5](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

Reaching into deeply nested object graphs tightly couples the caller to the
entire path structure. Refactor callers to interact directly with immediate
dependencies.

### Comprehensive Example

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
