# `struct AnyPublisher`

`@frozen struct AnyPublisher<Output, Failure> where Failure: Error`

Publisher that performs type erasure by wrapping another publisher. Concrete implementation of `Publisher` that has no significant properties of its own and passes through values and completion event from its upstream publisher.

Use `AnyPublisher` to wrap publisher (with `eraseToAnyPublisher()` operator) whose type has details that shouldn't be exposed across API boundaries, such as different modules. Wrapping `Subject` with `AnyPublisher` also prevents callers from accessing its `send(_:)`. Allows changing underlying publisher implementation over time without affecting clients.
