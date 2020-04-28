# `protocol Subscriber`

Type that can receive input from publisher.

Receives stream of elements from `Publisher`, along with life cycle events describing changes to their relationship. Its associated types `Input` and `Failure` must match publisher's `Output` and `Failure`.

Connect subscriber to publisher by calling publisher's `subscribe(_:)`. Then, publisher invokes subscriber's `receive(subscription:)` to give subscriber `Subscription`, which subscriber uses to demand elements from, or to cancel subscription.

After subscriber makes initial demand, publisher calls `receive(_:)`, possibly asynchronously, to deliver newly-published elements. If publisher stops publishing, it calls `receive(completion:)`, using param of type `Subscribers.Completion` to indicate publishing completes normally or with error.

![publisher subscriber flow](../../../assets/Combine/publisher_subscriber_flow.png)

Combine provides following subscribers as operators on `Publisher` type:

* `sink(receiveCompletion:receiveValue:)` executes closures when it receives completion signal and each time it receives new element.
* `assign(to:on:)` writes each new value to prop of key path.
