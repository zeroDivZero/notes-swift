# `protocol Publisher`

Type that can send sequence of values over time.

Has 2 associated types: `Output` and `Failure`.

Types of sent values:

* Output value of `Output` type. 0 or more.
* Successful completion. At most 1, then stops sending.
* Failure error of `Failure` type. At most 1, then stops sending.

Some `Foundation` types, including `Timer` and `URLSession`, expose their functionality through publishers.

## Send Values

Sends values to one or more `Subscriber` instances. Subscriber's `Input` and `Failure` associated types must match `Output` and `Failure` types of publisher. Publisher implements `receive(subscriber:)` to accept subscriber. Then, publisher can call following methods on subscriber:

* `receive(subscription:)`: Acknowledges subscribe request and returns `Subscription` instance. Subscriber uses subscription to demand elements from publisher or cancel publishing.
* `receive(_:)`: Sends one value to subscriber.
* `receive(completion:)`: Informs subscriber that publishing has ended, normally or with error.

Extensions on `Publisher` define many composable operators for creating value-processing chains. Each operator returns type that implements `Publisher`. Most of these types exist as extensions on `enum Publishers`. E.g., `map(_:)` operator returns instance of `Publishers.Map`.

## Create Custom Publisher

Instead of implementing `Publisher`, create publisher with one of following types provided by Combine:

* Use concrete subclass of `Subject`, such as `PassthroughSubject`, to publish values on-demand by calling its `send(_:)`.
* Use `CurrentValueSubject` to publish when updating subject's underlying value.
* Add `@Published` annotation to property. It gains publisher that sends value when it changes.
