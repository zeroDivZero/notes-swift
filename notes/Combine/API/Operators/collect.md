# `collect()`

Collects up to specified number of values, then emits single array of collection.

![collect publisher flow marble diagram](../../../../assets/Combine/publisher_collect.png)

```swift
[1, 2, 3, 4, 5].publisher
    .collect(2)
    .sink { print($0) }
// [1, 2]
// [3, 4]
// [5]
```

If no threshold specified, returns all values or error. Warning: uses unbounded memory.
