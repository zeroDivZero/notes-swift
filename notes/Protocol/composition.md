# COMPOSITION

Besides inheritance, another way to organize specialized protocols. Ex: `Operation` protocol to handle async operations:

```swift
protocol Operation {
    associatedtype Input
    associatedtype Output

    func prepare()
    func cancel()
    func perform(with input: Input,
                 then handler: @escaping (Output) -> Void)
}
```

Large protocol, can lead to redundant implementations (operations that can't be canceled or don't need be prepared still need to implement those methods). Decompose:

```swift
protocol Preparable {
    func prepare()
}

protocol Cancellable {
    func cancel()
}

protocol Performable {
    associatedtype Input
    associatedtype Output

    func perform(with input: Input,
                 then handler: @escaping (Output) -> Void)
}
```

Similar to `Codable` (`Decodable & Encodable`), use **typealias** to compose:

```swift
typealias Operation = Preparable & Cancellable & Performable
```

Can selectively conform to specific aspects, and smaller, building-block-style protocol is easier to reuse and test:

```swift
extension Sequence where Element == Cancellable {
    func cancelAll() {
        forEach { $0.cancel() }
    }
}
```
