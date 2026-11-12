# Where Bugs Hide

A menu of bug families, not a checklist to report against. For each changed
function, pick the families that fit what it does and try their values.

## Logic

- Inverted or incomplete conditions, `&&` vs `||`, a negation lost in a
  refactor, a `when`/`switch` branch that catches more than intended.
- Off-by-one at each bound: inclusive vs exclusive, index vs count, first and
  last page, loop that skips or repeats an item.
- Wrong variable: copy-paste left the old name, the parameter is shadowed, the
  stale copy is used instead of the updated one.
- A value computed but never used, or used before it is updated.
- The change fixes one call site but a sibling path still takes the old route.

## Values and Collections

- Null, absent, default, zero, negative, maximum, overflow; float rounding and
  money.
- Empty, one, many, duplicates, unsorted input; an item added or removed while
  iterating.
- Unit or type mismatches across a boundary: ms vs s, px vs dp, bytes vs
  characters, cents vs units, id of one entity passed as another.
- Text: empty, blank, very long, emoji, characters that need escaping, locale-
  dependent formatting and parsing, encoding.
- Time: dates vs instants, time zones and daylight saving, month and year ends.

## Errors and Failure Paths

- An exception swallowed, a `catch` too broad, a fallback that hides the
  failure and returns a plausible but wrong value.
- State left half-written when a step fails: partial saves, flags not reset,
  loading spinners that never stop.
- Resources not released on the error path: cursors, streams, listeners,
  subscriptions, locks.
- Retries that are not idempotent; error results that callers ignore.

## State, Lifecycle and Concurrency

- Called twice, before init, after dispose or cancel; a second tap while the
  first request runs.
- A response or callback arriving after the screen, scope or user changed.
- Races on shared mutable state, check-then-act, work on the wrong thread or
  dispatcher.
- Caches, memoized or `remember`ed values that keep a stale input.
- Data written by the previous app or schema version and read by the new one
  (migrations, persisted preferences, cached payloads).

## Contracts and Boundaries

- The caller's expectation vs what the callee now returns: nullability,
  ordering, range, units, thrown exceptions.
- API, database, serializer and UI component on both sides of a value: they
  must agree on type, format and meaning.
- Each `actual` of an `expect`, each platform branch, each implementation of
  an interface: one of them often behaves differently.
- Framework or library behavior assumed but not true for this version or
  platform (defaults, threading, lifecycle, rounding).
