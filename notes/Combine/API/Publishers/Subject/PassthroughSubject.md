# `class PassthroughSubject`

Concrete implementation of `Subject`.

Unlike `CurrentValueSubject`, `PassthroughSubject` doesn't have initial value or buffer of most recently published element. `PassthroughSubject` drops values if there are no subscribers, or its current demand is zero.

```swift
let aPublisher = PassthroughSubject<Int, SomeError>()
let subscriber = aPublisher
    .filter { $0 > 25 }
    .sink(receiveCompletion: { _ in },
          receiveValue: {
            print("High value: \($0)")
          })
```
