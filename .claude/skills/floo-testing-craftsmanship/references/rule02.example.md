# Rule 2: Structure Unit Tests with SETUP, RUN, ASSERT — Reference Examples

Deep dive for [SKILL.md — Rule 2](../SKILL.md).

______________________________________________________________________

## AAA (Arrange-Act-Assert) Structural Pattern

Explicit section markers ensure readable unit tests across engineering teams.

### Comprehensive Example

```kotlin
@Test
fun `given user with insufficient balance - on withdraw - it should return InsufficientFundsResult`() {
    // SETUP
    val accountId = "ACC_9912"
    val initialBalance = 50.00
    val withdrawalAmount = 100.00
    val accountRepository = mock<AccountRepository>()
    whenever(accountRepository.findBalance(accountId)).thenReturn(initialBalance)
    val service = BankingService(accountRepository)

    // RUN
    val result = service.withdraw(accountId, withdrawalAmount)

    // ASSERT
    assertTrue(result is WithdrawalResult.Failure.InsufficientFunds)
    verify(accountRepository, never()).updateBalance(any(), any())
}
```
