# `struct Just`

Publisher that emits output once, then finishes.

```swift
struct Just<Output>
```

```swift
Just(24)
    .map { $0 < 21 ? "young" : "adult" }
    .sink { print($0) }
```

Use `Just` to start chain of publishers. Also useful when replacing value with `Catch`.

`Just` can't fail, unlike `Result.Publisher`, and it always produces value, unlike `Optional.Publisher`.
