# `struct Empty`

publisher that never publishes any values, and optionally finishes immediately.

```swift
struct Empty<Output, Failure> where Failure: Error
```

Can create "Never" publisher — one which never sends values and never finishes or fails — with initializer `Empty(completeImmediately: false)`.
