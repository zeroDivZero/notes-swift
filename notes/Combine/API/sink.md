# `sink()`

`Publisher` method to connect simple subscriber. Creates and attaches subscriber with closure-based behavior. Immediately requests unlimited number of values before returning the subscriber.

```swift
[1, 2, 3]
    .publisher
    .sink(receiveCompletion: { completion in
        switch completion {
        // can ignore .failure because publisher never fails
        case .finished:
            print("Received Completion")
        }
    }, receiveValue: { value in
        print("Received value \(value)")
    })
```

Even simpler version with only `receiveValue:` closure if publisher's `Failure` is `Never`.

```swift
[1, 2, 3]
    .publisher
    .sink { value in
        print("Received value \(value)")
    }
```
