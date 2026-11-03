# Rule 13: Prefer Structured Concurrency Over Callbacks — Reference Examples

Deep dive for [SKILL.md — Rule 13](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

A closure parameter used to deliver an asynchronous result is an implicit,
anonymous contract. It hides the control flow, cannot be cancelled by the
caller, has no natural error channel, and grows a new parameter for every new
concern (`onError`, `onStart`, `onProgress`…). Structured concurrency moves the
asynchrony into the return type: the caller controls the lifecycle, errors
propagate through `throw`, and the signature documents exactly what is
produced.

This is an application of the Dependency Inversion Principle (Rule 6): explicit,
named contracts instead of anonymous callbacks. It is also the standard remedy
for "callback hell".

The same reasoning applies to closures stored as dependencies, even when they
are synchronous. A factory injected as `() -> HttpClient` (by hand or through
DI) is still an anonymous contract: it cannot be searched for or documented,
two factories with the same signature need qualifiers to be told apart in a DI
graph, and a test has to hand-roll a capturing lambda to count calls or control
what each call returns. A named factory type (`fun interface` in Kotlin,
`protocol` in Swift) keeps the lambda syntax at the call site while giving the
dependency a name, a contract, and a natural fake.

| Case                                                    | Expected shape                                                      |
| ------------------------------------------------------- | ------------------------------------------------------------------- |
| Single result                                           | `suspend fun` (Kotlin) / `async throws` (Swift)                     |
| Stream of events (progress, updates, observation)       | `Flow<T>` (Kotlin) / `AsyncStream<T>` / `AsyncThrowingStream<T, E>` |
| Synchronous closures (`map`, `filter`, config, DSL)     | OK                                                                  |
| UI events (Compose, SwiftUI `onClick`, `onValueChange`) | OK and idiomatic                                                    |
| Callback-based third-party library (e.g. Ktor)          | Consumed internally, wrapped to expose a `Flow` / `AsyncStream`     |
| Factory or provider stored as a dependency (incl. DI)   | Named factory type: `fun interface` (Kotlin) / `protocol` (Swift)   |

## Kotlin

### ❌ DON'T

```kotlin
fun getUser(id: String, onResult: (User) -> Unit, onError: (Throwable) -> Unit)

fun download(url: String, onProgress: (Float) -> Unit, onDone: (File) -> Unit)
```

### ✅ DO

Single result:

```kotlin
suspend fun getUser(id: String): User
```

Stream of events:

```kotlin
fun download(url: String): Flow<DownloadState>

sealed interface DownloadState {
    data class Progress(val percent: Float) : DownloadState
    data class Done(val file: File) : DownloadState
}
```

Wrapping a callback-based library (Ktor):

```kotlin
fun download(url: String): Flow<DownloadState> = channelFlow {
    val bytes = client.get(url) {
        onDownload { sent, total ->
            total?.let { send(DownloadState.Progress(sent.toFloat() / it)) }
        }
    }.readRawBytes()
    send(DownloadState.Done(saveToFile(bytes)))
}
```

Factory stored as a dependency:

```kotlin
// ❌ Anonymous contract, even when provided by DI
class SyncWorker(private val createClient: () -> HttpClient)

// ✅ Named contract, still constructible with a lambda
fun interface HttpClientFactory {
    fun create(): HttpClient
}

class SyncWorker(private val clientFactory: HttpClientFactory)

// Test
val worker = SyncWorker { FakeHttpClient() }
```

UI events stay as closures in Compose:

```kotlin
@Composable
fun SaveButton(onClick: () -> Unit)
```

## Swift

### ❌ DON'T

```swift
func fetchUser(id: String, completion: @escaping (Result<User, Error>) -> Void)
```

### ✅ DO

Single result:

```swift
func fetchUser(id: String) async throws -> User
```

Stream of events:

```swift
func download(url: URL) -> AsyncThrowingStream<DownloadState, Error>
```

Factory stored as a dependency:

```swift
// ❌
final class SyncWorker {
    private let makeClient: () -> HTTPClient
}

// ✅
protocol HTTPClientFactory {
    func make() -> HTTPClient
}

final class SyncWorker {
    private let clientFactory: HTTPClientFactory
}
```

UI events stay as closures in SwiftUI:

```swift
struct SaveButton: View {
    let onTap: () -> Void
}
```

## Summary

> **If a closure exists to receive an asynchronous result, it must become a
> return type. If a closure is stored as a dependency, it must become a named
> type.**
