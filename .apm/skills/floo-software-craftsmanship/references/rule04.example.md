# Rule 4: Amdahl's Law — Reference Examples

Deep dive for [SKILL.md — Rule 4](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

Amdahl's law demonstrates that $S\_{\\text{latency}}(s) = \\frac{1}{(1 - p) +
\\frac{p}{s}}$, where $p$ is the proportion of parallelizable execution and $s$
is the speedup factor. Sequential bottlenecks quickly limit performance gains.

### Comprehensive Example

**❌ DON'T (Optimizing parallel worker count while synchronous file I/O blocks
queue)**

```kotlin
// Launching 64 coroutines to write log lines to a single non-concurrent FileOutputStream
val executor = Executors.newFixedThreadPool(64)
repeat(10_000) { id ->
    executor.submit {
        synchronized(fileLock) {
            fileStream.write("Log $id\n".toByteArray())
        }
    }
}
```

**✅ DO (Eliminate sequential bottlenecks using async channels or batching
first)**

```kotlin
// Channel converts lock contention into sequential batch writes on single background worker
val logChannel = Channel<String>(capacity = 10_000)
// Single writer coroutine processes non-blocking batch writes
```
