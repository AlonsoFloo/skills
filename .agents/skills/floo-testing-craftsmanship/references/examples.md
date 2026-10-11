# Testing Craftsmanship - Deep Dives & Reference Examples

This document provides detailed examples, test pattern structures, and reactive
stream test guidance for [SKILL.md](../SKILL.md).

______________________________________________________________________

## Rule 1: Standardize Unit Test Naming Conventions

### Behavioral Naming Pattern

Using BDD-inspired naming
(`given <context> - on <action> - it should <expected behavior>`) clarifies
expectations when tests fail in CI logs.

#### Comprehensive Examples

```kotlin
// Edge cases & domain failure scenarios
@Test
fun `given expired session token - on refresh token - it should throw UnauthorizedException`() { ... }

@Test
fun `given empty search query - on search - it should emit cached default results`() { ... }

@Test
fun `given network disconnection - on order checkout - it should retry 3 times before failing`() { ... }
```

______________________________________________________________________

## Rule 2: Structure Unit Tests with SETUP, RUN, ASSERT

### AAA (Arrange-Act-Assert) Structural Pattern

Explicit section markers ensure readable unit tests across engineering teams.

#### Comprehensive Example

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

______________________________________________________________________

## Rule 3: Use Turbine for Testing Kotlin Flows

### Reactive Stream Assertion Guidance

Turbine isolates state emission streams, allowing item-by-item verification
without thread race conditions.

#### Comprehensive Example

```kotlin
@Test
fun `given search input stream - on typing query - it should debounce and emit search results`() = runTest {
    // SETUP
    val searchApi = mock<SearchApi>()
    whenever(searchApi.search("Kotlin")).thenReturn(listOf("Kotlin Coroutines", "Kotlin Multiplatform"))
    val viewModel = SearchViewModel(searchApi)

    // RUN & ASSERT
    viewModel.searchResults.test {
        assertEquals(emptyList(), awaitItem()) // Initial state

        viewModel.onQueryChanged("K")
        viewModel.onQueryChanged("Kot")
        viewModel.onQueryChanged("Kotlin")

        advanceTimeBy(300) // Advance past debounce threshold

        val result = awaitItem()
        assertEquals(2, result.size)
        assertEquals("Kotlin Coroutines", result[0])

        cancelAndIgnoreRemainingEvents()
    }
}
```
