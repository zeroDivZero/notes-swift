# `struct Record`

Publisher that records series of inputs and completion, for later playback to each subscriber.

```swift
struct Record<Output, Failure> where Failure: Error
```
