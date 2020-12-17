# RECEIVE AND HANDLE EVENTS

## Connect Publisher and Subscriber

Publisher of text field change notifications:

```swift
let pub = NotificationCenter.default
    .publisher(for: NSControl.textDidChangeNotification, object: filterField)
```

`Subscriber` receives elements from publisher. Subscriber defines associated type `Input` for type it receives. Publisher defines `Output` for type it publishes. Both define `Failure` for error type they publish/receive. To connect subscriber to publisher, `Output` must match `Input`, and `Failure` types must match.

2 built-in subscribers automatically match output and failure types:

1. `sink(receiveCompletion:receiveValue:)` takes 2 closures. 1st executes when received `Subscribers.Completion` (enum indicating publisher finished normally or failed). 2nd executes on each element received.

2. `assign(to:on:)` assigns element to key-pathed property.

E.g., log when publisher completes, and each time it receives element:

```swift
let sub = NotificationCenter.default
    .publisher(for: NSControl.textDidChangeNotification, object: filterField)
    .sink(receiveCompletion: { print($0) },
          receiveValue: { print($0) })
```

Both subscribers request unlimited number of elements. To control rate of receiving elements, create own subscriber by implementing `Subscriber` protocol.

## Change `Output` with Operators

Sometimes type published inconvenient. E.g., `NotificationCenter.Publisher.Output` (`Notification`) not easy to use if only need text field's string value. Use operators like `map(_:)`, `flatMap(maxPublishers:_:)`, and `reduce(_:_:)` to change output type.

```swift
let sub = NotificationCenter.default
    .publisher(for: NSControl.textDidChangeNotification, object: filterField)
    .map({ ($0.object as! NSTextField).stringValue })
    .assign(to: \MyViewModel.filterString, on: myViewModel)
```

## Customize Publishers with Operators

3 example operators to customize and further improve event chain:

1. Use `filter(_:)` to ignore input meeting certain criteria.

2. If filtering is expensive — e.g., querying large database — wait for user to stop typing. `debounce(for:scheduler:options:)` waits minimum lapse time before emitting event. `RunLoop` class provides conveniences for specifying time delay in sec or ms.

3. If results update UI, deliver callbacks to main thread by using `receive(on:options:)`.

```swift
let sub = NotificationCenter.default
    .publisher(for: NSControl.textDidChangeNotification, object: filterField)
    .map({ ($0.object as! NSTextField).stringValue })
    .filter({ $0.unicodeScalars.allSatisfy({ CharacterSet.alphanumerics.contains($0) }) })
    .debounce(for: .milliseconds(500), scheduler: RunLoop.main)
    .receive(on: RunLoop.main)
    .assign(to:\MyViewModel.filterString, on: myViewModel)
```

## Cancel Publishing when Desired

Publisher continues to emit elements until it completes normally or fails. To stop subscribing to publisher any time, cancel subscription. `sink()` and `assign()` subscribers implement `Cancellable` protocol, which provides `cancel()`.

For custom `Subscriber`, publisher sends `Subscription` object when first subscribed. Store this object, then call its `cancel()` later. Custom subscriber should implement `Cancellable`, and forward cancel to stored subscription.
