# CUSTOM PUBLISHER

Sometimes need to create publisher ground up; e.g., publisher is tied to object have complete control over.

Instead of using traditional target/action pattern, create publisher that works for any `UIControl` to publish events:

```swift
extension UIControl {

    func publisher(for event: Event) -> EventPublisher {
        EventPublisher(control: self, event: event)
    }

    // must specify Output and Failure types,
    // and implement receive(subscriber:) to conform to Publisher
    struct EventPublisher: Publisher {
        typealias Output = Void
        typealias Failure = Never

        fileprivate var control: UIControl
        fileprivate var event: Event

        // called when new object starts observing
        // need to create subscription and attach it to new subscriber
        func receive<S: Subscriber>(
            subscriber: S
        ) where S.Input == Output, S.Failure == Failure {
            // create subscription, which holds reference to subscriber
            let subscription = EventSubscription<S>()
            subscription.target = subscriber

            // attach subscription to subscriber
            subscriber.receive(subscription: subscription)

            // connect subscription to observed control
            control.addTarget(subscription,
                              action: #selector(subscription.trigger),
                              for: event
            )
        }
    }

    // must implement request(_:) and cancel() to conform to Subscription
    private class EventSubscription<Target: Subscriber>: Subscription where Target.Input == Void {
        var target: Target?

        // this subscription doesn't respond to demand, since it
        // only emits events according to its underlying UIControl
        // instance; implement this only to conform to Subscription
        func request(_ demand: Subscribers.Demand) {}

        func cancel() {
            // simply nil out target so it stops receiving
            target = nil
        }

        @objc func trigger() {
            // when underlying UIControl triggers event,
            // pass Void to target to emit it
            target?.receive(())
        }
    }

}
```

Can call `publisher(for:)` directly, or further design specialized APIs:

```swift
let button = UIButton()
let subscription = button.publisher(for: .touchUpInside).sink {
    // handle tap
}

extension UIButton {
    var tapPublisher: EventPublisher {
        publisher(for: .touchUpInside)
    }
}

extension UITextField {
    var textPublisher: AnyPublisher<String, Never> {
        publisher(for: .editingChanged)
            .map { self.text ?? "" }
            .eraseToAnyPublisher()
    }
}
```

Combine allows merging streams:

```swift
nameTextField.textPublisher.combineLatest(
    addressTextField.textPublisher,
    cityTextField.textPublisher
)
    .map(ShippingInfo.init)
    .sink { [weak self] in
        self?.shippingInfo = $0
    }
    .store(in: &cancellables)
```

With `combineLatest()`, no combined value will be emitted before each of publishers has sent at least one value.

With iOS 14, simplify further with new `assign()` variant that takes published property, and no need to store `Cancellable` token:

```swift
nameTextField.textPublisher.combineLatest(
    addressTextField.textPublisher,
    cityTextField.textPublisher
)
    .map(ShippingInfo.init)
    .assign(to: &$shippingInfo)
```
