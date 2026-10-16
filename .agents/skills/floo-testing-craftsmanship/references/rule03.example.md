# Rule 3: Use Turbine for Testing Kotlin Flows — Reference Examples

Deep dive for [SKILL.md — Rule 3](../SKILL.md).

______________________________________________________________________

## Reactive Stream Assertion Guidance

Turbine isolates state emission streams, allowing item-by-item verification
without thread race conditions.

### Comprehensive Example

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
