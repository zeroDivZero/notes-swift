# `struct Deferred`

Publisher that awaits subscription before running supplied closure to create publisher for new subscriber.

```swift
struct Deferred<DeferredPublisher> where DeferredPublisher: Publisher
```
