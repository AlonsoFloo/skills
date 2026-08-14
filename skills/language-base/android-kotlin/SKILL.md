---
name: android-kotlin
description: Android & Kotlin engineering
---

# Android Kotlin

> Android & Kotlin engineering

This skill is derived from the **Core Engineering Skills** specification supplied with this repository.
Treat the rules below as engineering guidance for AI coding assistants, code reviewers, and engineers.
When a rule is context-dependent, prefer explicit domain reasoning over mechanical application.

## Rule 16: Never Duplicate UI State Variables

**Description:** A single property within the unified UI state object must serve as the sole source of truth. Duplicating state variables between the ViewModel and the UI state creates state drift and UI rendering bugs.

**❌ DON'T**

```kotlin
data class UserListState(
    val users: List<User> = emptyList(),
    val searchQuery: String = "",
)

class UserListViewModel : ViewModel() {
    // ❌ Duplicating text field state in ViewModel property AND in UI state!
    var searchQueryInput: String = ""

    private val _uiState = MutableStateFlow(UserListState())
    val uiState: StateFlow<UserListState> = _uiState.asStateFlow()

    fun onQueryChanged(newQuery: String) {
        searchQueryInput = newQuery // Updating local property
        _uiState.update { it.copy(searchQuery = newQuery) } // Updating state object—dual sources of truth!
    }
}
```

**✅ DO**

```kotlin
class UserListViewModel : ViewModel() {
    // ✅ Single source of truth contained entirely within the UI state stream
    private val _uiState = MutableStateFlow(UserListState())
    val uiState: StateFlow<UserListState> = _uiState.asStateFlow()

    fun onQueryChanged(newQuery: String) {
        _uiState.update { it.copy(searchQuery = newQuery) }
    }
}
```

## Rule 17: Strictly Follow Modern Android Standard Architecture

**Description:** Android codebases must strictly adhere to **Unidirectional Data Flow (UDF)**.

1. **UI Layer**: Observes state from the ViewModel and sends UI events. Holds no business logic.
1. **State Holder**: (ViewModel) Exposes immutable `StateFlow`, handles UI events.
1. **Domain Layer**: Encapsulates complex business rules.
1. **Data Layer**: Single source of truth for data.

**❌ DON'T**

```kotlin
@Composable
fun UserScreen() {
    var user by remember { mutableStateOf<User?>(null) }
    LaunchedEffect(Unit) {
        // Direct API call in the UI! No ViewModel, no Repository, no UDF.
        user = RetrofitClient.api.fetchUser()
    }
    Text(text = user?.name ?: "Loading...")
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

## Rule 18: Stateless ViewModifiers Belong in Extension Helpers (Compose)

**Description:** Creating a `@Composable` modifier component that holds no internal state adds unnecessary framework allocation overhead. Plain stateless modifier chains should be declared as standard extension functions.

**❌ DON'T**

```kotlin
// Jetpack Compose: Stateless modifier declared as a @Composable function
@Composable
fun Modifier.defaultPadding(): Modifier = this.padding(16.dp)
```

**✅ DO**

```kotlin
// Jetpack Compose: Standard extension function for stateless modifiers
fun Modifier.defaultPadding(): Modifier = this.padding(16.dp)

// Jetpack Compose: @Composable modifier IS warranted when managing state & animations
@Composable
fun Modifier.shake(trigger: Boolean): Modifier {
    val offsetX = remember { Animatable(0f) }
    // ... animation logic
    return this.offset { IntOffset(offsetX.value.roundToInt(), 0) }
}
```

______________________________________________________________________

## 🍏 DOMAIN: IOS & SWIFT
