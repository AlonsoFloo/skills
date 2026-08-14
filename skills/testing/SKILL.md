---
name: testing
description: Testing conventions & reactive stream testing
---

# Testing

> Testing conventions & reactive stream testing

This skill is derived from the **Core Engineering Skills** specification supplied with this repository.
Treat the rules below as engineering guidance for AI coding assistants, code reviewers, and engineers.
When a rule is context-dependent, prefer explicit domain reasoning over mechanical application.

## Rule 13: Standardize Unit Test Naming Conventions

**Description:** Unit tests serve as living documentation. Test names must be highly descriptive and follow a strict behavioral format: `given <context> - on <action> - it should <expected behavior>`.

**❌ DON'T**

```kotlin
@Test
fun testUserLogin() { ... }

@Test
fun login_failure_shows_error() { ... }
```

**✅ DO**

```kotlin
@Test
fun `given valid credentials - on login - it should emit success state`() { ... }

@Test
fun `given network timeout - on fetch data - it should throw TimeoutException`() { ... }
```

## Rule 14: Structure Unit Tests with SETUP, RUN, ASSERT

**Description:** Every unit test must be visually divided into three distinct phases using explicit block comments: `// SETUP`, `// RUN`, and `// ASSERT`.

**❌ DON'T**

```kotlin
@Test
fun `given active user - on get profile - it should return profile data`() {
    val mockRepo = mock<UserRepository>()
    val viewModel = UserViewModel(mockRepo)
    whenever(mockRepo.getUser()).thenReturn(mockUser)
    viewModel.loadProfile()
    assertEquals(mockUser, viewModel.uiState.value.user)
    verify(mockRepo).getUser()
}
```

**✅ DO**

```kotlin
@Test
fun `given active user - on get profile - it should return profile data`() {
    // SETUP
    val mockUser = User(id = "123", name = "Alice")
    val mockRepo = mock<UserRepository>()
    whenever(mockRepo.getUser()).thenReturn(mockUser)
    val viewModel = UserViewModel(mockRepo)

    // RUN
    viewModel.loadProfile()

    // ASSERT
    val currentState = viewModel.uiState.value
    assertEquals(mockUser, currentState.user)
    verify(mockRepo, times(1)).getUser()
}
```

## Rule 15: Use Turbine for Testing Kotlin Flows

**Description:** Testing asynchronous reactive streams (like `Flow` or `StateFlow` in Kotlin) using raw coroutine builders, manual lists, or `delay()` is flaky and leads to race conditions. When available, always use the **Turbine** library to test Flows sequentially.

**❌ DON'T**

```kotlin
@Test
fun `given data - on load - it should emit loading then success`() = runTest {
    // SETUP
    val emittedStates = mutableListOf<UiState>()
    val job = launch { viewModel.uiState.toList(emittedStates) }

    // RUN
    viewModel.loadData()
    advanceUntilIdle()

    // ASSERT
    assertEquals(UiState.Loading, emittedStates[0])
    assertEquals(UiState.Success, emittedStates[1])
    job.cancel()
}
```

**✅ DO**

```kotlin
@Test
fun `given data - on load - it should emit loading then success`() = runTest {
    // SETUP
    val viewModel = MyViewModel(repository)

    // RUN & ASSERT combined cleanly with Turbine
    viewModel.uiState.test {
        assertEquals(UiState.Idle, awaitItem()) // Initial state

        viewModel.loadData() // Trigger action

        assertEquals(UiState.Loading, awaitItem())
        assertEquals(UiState.Success, awaitItem())

        cancelAndIgnoreRemainingEvents()
    }
}
```

______________________________________________________________________

## 📱 DOMAIN: ANDROID & KOTLIN
