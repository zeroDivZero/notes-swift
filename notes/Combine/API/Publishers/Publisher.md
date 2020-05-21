# `protocol Publisher`

Type that can transmit sequence of values over time.

Delivers elements to one or more `Subscriber` instances. Subscriber's `Input` and `Failure` associated types must match `Output` and `Failure` types of publisher. Publisher implements `receive(subscriber:)` to accept subscriber. Then, publisher can call following methods on subscriber:

* `receive(subscription:)`: Acknowledges subscribe request and returns `Subscription` instance. Subscriber uses subscription to demand elements from publisher or cancel publishing.
* `receive(_:)`: Delivers one element to subscriber.
* `receive(completion:)`: Informs subscriber that publishing has ended, normally or with error.

Every Publisher must adhere to this contract for downstream subscribers to function correctly.

Extensions on `Publisher` define many composable operators for creating event-processing chains. Each operator returns type that implements `Publisher`. Most of these types exist as extensions on `enum Publishers`. E.g., `map(_:)` operator returns instance of `Publishers.Map`.

## Create Custom Publisher

Instead of implementing `Publisher`, create publisher with one of following types provided by Combine:

* Use concrete subclass of `Subject`, such as `PassthroughSubject`, to publish values on-demand by calling its `send(_:)`.
* Use `CurrentValueSubject` to publish when updating subject's underlying value.
* Add `@Published` annotation to property. It gains publisher that emits event when value changes.
