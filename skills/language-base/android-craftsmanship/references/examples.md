# Android Craftsmanship - Deep Dives & Reference Examples

This document provides extended examples and pattern references for [SKILL.md](../SKILL.md).

---

## Rule 1: Never Duplicate UI State Variables

### Single Source of Truth
ViewModel state should be driven entirely through explicit data streams like `StateFlow`.

```kotlin
data class UserListUiState(
    val users: List<User> = emptyList(),
    val searchQuery: String = "",
    val isLoading: Boolean = false,
    val errorMessage: String? = null
)

class UserListViewModel(
    private val userRepository: UserRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(UserListUiState())
    val uiState: StateFlow<UserListUiState> = _uiState.asStateFlow()

    fun onSearchQueryChanged(query: String) {
        _uiState.update { it.copy(searchQuery = query) }
        fetchUsers(query)
    }

    private fun fetchUsers(query: String) {
        viewModelScope.launch {
            _uiState.update { it.copy(isLoading = true) }
            runCatching { userRepository.searchUsers(query) }
                .onSuccess { users -> _uiState.update { it.copy(users = users, isLoading = false) } }
                .onFailure { err -> _uiState.update { it.copy(errorMessage = err.message, isLoading = false) } }
        }
    }
}
```

---

## Rule 2: Strictly Follow Modern Android Standard Architecture

### Layer Responsibilities
- **UI Layer**: Jetpack Compose screens rendering state and delegating user interactions to ViewModels.
- **ViewModel Layer**: Handles events, transforms domain state into immutable UI state streams.
- **Domain Layer**: Contains pure Kotlin UseCases encapsulating complex business requirements.
- **Data Layer**: Repositories managing offline caching (Room) and remote synchronization (Retrofit).
