# COMBINE BASICS

FRP framework modeling async events and operations as *values over time*.

## Publisher

Observable type that emits sequence of values (when events occur) over time. Active indefinitely or eventually completes (successfully or fails with error).

Some core libs support Combine. E.g., `URLSession` supplies publisher to wrap data task:

```swift
let url = URL(string: "https://api.github.com/repos/johnsundell/publish")!
let publisher = URLSession.shared.dataTaskPublisher(for: url)
```

## Subscriber

Publisher, unless subscribed, does not emit value. Attach subscriber with methods like `sink()` - specify closures to be invoked when new value received or publisher completed:

```swift
let cancellable = publisher.sink(
    receiveCompletion: { completion in
        // called once, when publisher completed
        switch completion {
        case .failure(let error):
            print(error)
        case .finished:
            print("success")
        }
    },
    receiveValue: { value in
        // called when new value emitted, can be called multiple times
        print(value)
    }
)
```

`sink()` returns `AnyCancellable`. When attaching subscriber, publisher returns `Cancellable` object that acts as token for subscription. Retain this for as long as subscription should remain active. When deallocated, subscription automatically canceled (cancel manually by calling `cancel()` on token).

## Operation Chain

Can write closure-based completion handler to decode data in `sink()`, but power of Combine is in composable operation chains:

```swift
struct Repository: Codable {
    var name: String
    var url: URL
}

let cancellable = publisher
    .map(\.data) // DataTaskPublisher publishes (data, response), only interested in `data`
    .decode(type: Repository.self, decoder: JSONDecoder())
    .sink(
        receiveCompletion: { completion in
            switch completion {
            case .failure(let error):
                print("error:", error.localizedDescription)
            case .finished:
                print("success")
            }
        },
        receiveValue: { repo in
            print(repo)
        }
    )
```

Aim to keep closure simple, prefer reactive chain that transforms value.

## Update UI

Combine handles mostly async values. E.g., `URLSession` works in background. If need to bind data to UI, emit values only to main thread with `receive()` operation:

```swift
let nameLabel = UILabel()
let errorLabel = UILabel()

let repoPublisher = publisher
    .map(\.data)
    .decode(type: Repository.self, decoder: JSONDecoder())
    .receive(on: DispatchQueue.main)

let cancellable = repoPublisher.sink(
    receiveCompletion: { completion in
        switch completion {
        case .failure(let error):
            errorLabel.text = error.localizedDescription
        case .finished:
            break
        }
    },
    receiveValue: { repo in
        nameLabel.text = repo.name
    }
)
```

## Other Foundation Types

There are other Foundation types that expose their functionality through publishers, such as `Timer` and `NotificationCenter`.

## `@Published`

Simple way to turn property into publisher. Publisher name is "$ + *property name*":

```swift
class Counter {
    // emits when new value assigned
    @Published private(set) var value = 0
    func increment() { value += 1 }
}

let counter = Counter()

let cancellable = counter.$value
    .filter { $0 > 2 }
    .sink {
        print("value: \(counter.value)")
        print("received: \($0)")
    }
counter.increment()
counter.increment()
counter.increment()  // value: 2; received: 3
counter.increment()  // value: 3; received: 4
```

Publishing occurs in property's `willSet`, `counter.value` not set yet when subscriber receives new value.

## `PassthroughSubject` / `CurrentValueSubject`

If `@Published` not applicable, use `Subject` - publisher that can emit by calling its `send()`.

```swift
class Counter {
    var publisher: AnyPublisher<Int, Never> {
        // "erase" subject's type info, exposing read-only publisher
        subject.eraseToAnyPublisher()
    }

    // private, can only send new values within class
    private let subject = PassthroughSubject<Int, Never>()

    private(set) var value = 0 {
        didSet { subject.send(value) }
    }

    func increment() {
        value += 1
    }
}
```

`CurrentValueSubject`, unlike `PassthroughSubject`, holds most recent published value in property `value` (can also be set directly instead of calling `send()`).

## Custom Publisher

If cannot use built-in publisher or one of previous techniques (often because no control over object's code), can create [own publisher](./8_custom_publisher.md).
