---
name: flo-android-craftsmanship
description: Android architecture, Kotlin UI state management & Jetpack Compose best practices
---

# Android Craftsmanship

> Modern Android architecture, Kotlin UI state management & Jetpack Compose
> craftsmanship

Treat the rules below as engineering guidance for AI coding assistants, code
reviewers, and software engineers. For comprehensive examples and deep dives,
see [references/examples.md](references/examples.md).

## Rule 1: Never Duplicate UI State Variables

**Description:** A single property within the unified UI state object must serve
as the sole source of truth. Duplicating state variables between ViewModel
properties and UI state object creates state drift and UI rendering bugs.

**❌ DON'T**

```kotlin
class UserListViewModel : ViewModel() {
    var searchQueryInput: String = "" // Duplicate local property!
    private val _uiState = MutableStateFlow(UserListState())
    val uiState: StateFlow<UserListState> = _uiState.asStateFlow()
}
```

**✅ DO**

```kotlin
class UserListViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(UserListState())
    val uiState: StateFlow<UserListState> = _uiState.asStateFlow()

    fun onQueryChanged(newQuery: String) {
        _uiState.update { it.copy(searchQuery = newQuery) }
    }
}
```

*See
[references/examples.md#rule-1-never-duplicate-ui-state-variables](references/examples.md#rule-1-never-duplicate-ui-state-variables)
for detailed reference cases.*

______________________________________________________________________

## Rule 2: Strictly Follow Modern Android Standard Architecture

**Description:** Android codebases must strictly adhere to **Unidirectional Data
Flow (UDF)** across UI, ViewModel, Domain, and Data layers.

**❌ DON'T**

```kotlin
@Composable
fun UserScreen() {
    var user by remember { mutableStateOf<User?>(null) }
    LaunchedEffect(Unit) {
        user = RetrofitClient.api.fetchUser() // Direct network call inside UI!
    }
}
```

**✅ DO**

```kotlin
@Composable
fun UserScreen(viewModel: UserViewModel = hiltViewModel()) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()

    when (val state = uiState) {
        is UserState.Loading -> CircularProgressIndicator()
        is UserState.Success -> Text(text = state.user.name)
    }
}
```

*See
[references/examples.md#rule-2-strictly-follow-modern-android-standard-architecture](references/examples.md#rule-2-strictly-follow-modern-android-standard-architecture)
for detailed reference cases.*

______________________________________________________________________

## Rule 3: Stateless ViewModifiers Belong in Extension Helpers (Compose)

**Description:** Creating a `@Composable` modifier function that holds no
internal state adds unnecessary framework allocation overhead. Plain stateless
modifier chains should be declared as standard extension functions.

**❌ DON'T**

```kotlin
@Composable
fun Modifier.defaultPadding(): Modifier = this.padding(16.dp)
```

**✅ DO**

```kotlin
fun Modifier.defaultPadding(): Modifier = this.padding(16.dp)
```

*See
[references/examples.md#rule-3-stateless-viewmodifiers-belong-in-extension-helpers-compose](references/examples.md#rule-3-stateless-viewmodifiers-belong-in-extension-helpers-compose)
for detailed reference cases.*
