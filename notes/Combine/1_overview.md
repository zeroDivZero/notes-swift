# OVERVIEW

Framework providing declarative API for processing values over time, and values can represent async events. Uses functional reactive programming (FRP) paradigm. Heavily used by SwiftUI.

Declares **publishers** to expose values that can change over time, and **subscribers** to receive them.

Publisher conforms to `Publisher` protocol. Chainable - has **operators** to transform values received from upstream publisher and republishes them.

At end of publisher chain, `Subscriber` acts on values as it receives them. Publisher only emits values when explicitly requested by subscriber. Subscriber in control of how fast it receives events.

Several Foundation types expose functionality through publishers, including `Timer`, `NotificationCenter`, and `URLSession`. Publisher also provided for KVO-compliant property.

Can combine output of multiple publishers and coordinate interaction. E.g., subscribe to updates from text field's publisher, and use text to perform URL requests. Then use another publisher to process responses to update UI.

Adopt Combine to make code easier to read and maintain, by centralizing event-processing code and eliminating techniques like nested closures and convention-based callbacks.

## Marble Diagram

Publisher flow commonly represented by _marble diagram_:

![publisher flow marble diagram](../../assets/Combine/publisher.png)

Each arrow is publisher. Marble is value emitted. Top arrow has line at end representing completion event. Bottom arrow has cross representing error event. After either event, no more value.

## When to Use

When something needs to react to variety of inputs, such as UI.

## Publisher and Subscriber

Can model value stream with array.

```swift
[1, 2, 3].publisher
    .sink(receiveCompletion: { completion in
        switch completion {
        case .failure(let error):
            print("Something went wrong: \(error)")
        case .finished:
            print("Received Completion")
        }
    }, receiveValue: { value in
        print("Received value \(value)")
    })
// Received value 1
// Received value 2
// Received value 3
// Received Completion
```

Combine adds `publisher` property to `Array`. Turns array of values into publisher to publish values to subscribers.

Created publisher type is `Publishers.Sequence<[Int], Never>`. `Publishers` is enum used as namespace for all publishers. Each publisher conforms to `Publisher`, but rarely created directly.

Generic types `[Int]` and `Never` mean publisher uses `[Int]` to publish values, and its failure type is `Never`, so it always completes successfully, never reaches `.failure` case in example `switch`.

Every publisher has `Output` and `Failure` associated types. `Output` is value type publisher pushes to subscribers. In example sequence publisher, `Output` is `Int`, `Failure` is `Never`. Fallible publisher often uses object conforming to `Error` as `Failure` type, but can specify another.

Subscribe to publisher with `sink(receiveCompletion:receiveValue:)`. Also special version if publisher cannot fail, only need to supply `receiveValue` closure. **Publisher only publishes values when there is subscriber**. `sink()` creates subscriber immediately and enables publisher to begin streaming values.

`receiveValue` closure called each time new value published, value received as its single arg.

## Tracking Subscriptions

Typically need to retain return type of `sink()`, or subscriber discarded when out of scope. Return type is `AnyCancellable`: type-erased wrapper around `Cancellable`. Can explicitly cancel subscription (`subscription.cancel()`), or automatically canceled when deallocated.

```swift
import Combine

var subscription: AnyCancellable?

func subscribe() {
    let notification = UIApplication.keyboardDidShowNotification
    let publisher = NotificationCenter.default.publisher(for: notification) // type: NotificationCenter.Publisher
    subscription = publisher.sink(receiveCompletion: { _ in // arg type: Subscribers.Completion<Never>, meaning Failure == Never
        print("Completion")
    }, receiveValue: { notification in // arg type: Notification, meaning Output == Notification
        print("Received notification: \(notification)")
    })
}

subscribe()
NotificationCenter.default.post(Notification(name: UIApplication.keyboardDidShowNotification))
```

## Transforming Publisher with Operator

Often don't use values emitted from publisher directly. Publishers can transform stream values with **operators** like `map` and `flatMap` (creates new publisher).

![map publisher flow marble diagram](../../assets/Combine/publisher_map.png)

New publisher type is `Publishers.Map<Upstream, Output>`. `Upstream` must be another publisher.

```swift
// transforms keyboard notification to keyboard height (CGFloat)
let publisher = NotificationCenter.default
    .publisher(for: UIResponder.keyboardDidShowNotification)
    .map { (notification) -> CGFloat in
        guard let endFrame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? NSValue else {
            return 0.0
        }

        return endFrame.cgRectValue.height
} // type: Publishers.Map<NotificationCenter.Publisher, CGFloat>
```
