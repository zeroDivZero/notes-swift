# `class PassthroughSubject`

Concrete implementation of `Subject`.

Unlike `CurrentValueSubject`, `PassthroughSubject` doesn't have initial value or buffer of most recently published element. `PassthroughSubject` drops values if there are no subscribers, or its current demand is zero.

```swift
import Combine

enum SomeError: Error {
    case terminated
}

let aPublisher = PassthroughSubject<Int, SomeError>()
let subscriber = aPublisher
    .filter { $0 > 25 }
    .sink(receiveCompletion: { _ in },
          receiveValue: {
            print("High value: \($0)")
          })

aPublisher.send(10)
aPublisher.send(25)
aPublisher.send(32)
aPublisher.send(completion: .failure(.terminated))

// output
// High value: 32
```
