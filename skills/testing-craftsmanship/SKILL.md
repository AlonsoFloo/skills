---
name: testing-craftsmanship
description: Testing conventions, behavioral naming, structured test layout & reactive stream testing
---

# Testing Craftsmanship

> Testing conventions, structured behavioral layout & reactive stream testing

Treat the rules below as engineering guidance for AI coding assistants, code reviewers, and software engineers.
For comprehensive examples and deep dives, see [references/examples.md](references/examples.md).

## Rule 1: Standardize Unit Test Naming Conventions

**Description:** Unit tests serve as living documentation. Test names must be highly descriptive and follow a strict behavioral format: `given <context> - on <action> - it should <expected behavior>`.

**❌ DON'T**
```kotlin
@Test
fun testUserLogin() { ... }
```

**✅ DO**
```kotlin
@Test
fun `given valid credentials - on login - it should emit success state`() { ... }
```

*See [references/examples.md#rule-1-standardize-unit-test-naming-conventions](references/examples.md#rule-1-standardize-unit-test-naming-conventions) for detailed reference cases.*

---

## Rule 2: Structure Unit Tests with SETUP, RUN, ASSERT

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

*See [references/examples.md#rule-2-structure-unit-tests-with-setup-run-assert](references/examples.md#rule-2-structure-unit-tests-with-setup-run-assert) for detailed reference cases.*

---

## Rule 3: Use Turbine for Testing Kotlin Flows

**Description:** Testing asynchronous reactive streams (like `Flow` or `StateFlow` in Kotlin) using raw coroutine builders, manual lists, or `delay()` is flaky and leads to race conditions. Always use the **Turbine** library to test Flows sequentially and deterministically.

**❌ DON'T**
```kotlin
@Test
fun `given data - on load - it should emit loading then success`() = runTest {
    val emittedStates = mutableListOf<UiState>()
    val job = launch { viewModel.uiState.toList(emittedStates) }
    viewModel.loadData()
    advanceUntilIdle()
    assertEquals(UiState.Loading, emittedStates[0])
    job.cancel()
}
```

**✅ DO**
```kotlin
@Test
fun `given data - on load - it should emit loading then success`() = runTest {
    // SETUP
    val viewModel = MyViewModel(repository)

    // RUN & ASSERT
    viewModel.uiState.test {
        assertEquals(UiState.Idle, awaitItem())
        viewModel.loadData()
        assertEquals(UiState.Loading, awaitItem())
        assertEquals(UiState.Success, awaitItem())
        cancelAndIgnoreRemainingEvents()
    }
}
```

*See [references/examples.md#rule-3-use-turbine-for-testing-kotlin-flows](references/examples.md#rule-3-use-turbine-for-testing-kotlin-flows) for detailed reference cases.*
