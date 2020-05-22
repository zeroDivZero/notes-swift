# `class AnyCancellable`

Type-erasing cancellable object that executes provided closure when canceled.

Subscriber implementations can use this type to provide "cancellation token" to let caller cancel publisher, but not to use `Subscription` to request items.

`AnyCancellable` inst automatically calls `cancel()` when deinitialized.
