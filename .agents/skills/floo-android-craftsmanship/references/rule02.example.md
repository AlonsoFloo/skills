# Rule 2: Strictly Follow Modern Android Standard Architecture — Reference Examples

Deep dive for [SKILL.md — Rule 2](../SKILL.md).

______________________________________________________________________

## Layer Responsibilities

- **UI Layer**: Jetpack Compose screens rendering state and delegating user
  interactions to ViewModels.
- **ViewModel Layer**: Handles events, transforms domain state into immutable UI
  state streams.
- **Domain Layer**: Contains pure Kotlin UseCases encapsulating complex business
  requirements.
- **Data Layer**: Repositories managing offline caching (Room) and remote
  synchronization (Retrofit).
