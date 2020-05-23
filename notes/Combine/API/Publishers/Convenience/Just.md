# `struct Just`

Publisher that emits output to each subscriber only once, and then finishes.

```swift
struct Just<Output>
```

Can use `Just` to start chain of publishers. Also useful when replacing value with `Catch`.

In contrast with `Result.Publisher`, `Just` can't fail. Unlike `Optional.Publisher`, `Just` always produces value.
