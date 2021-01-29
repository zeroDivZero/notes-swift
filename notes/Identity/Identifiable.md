# `protocol Identifiable`

```swift
protocol Identifiable {
    associatedtype ID: Hashable
    var id: ID { get }
}
```

Class of types whose instances hold value of entity with stable identity.

```swift
struct Person: Identifiable {
    var id: String
}

struct Website: Identifiable {
    var id: URL
}
```

If conforming class to `Identifiable`, default implementation provided using `ObjectIdentifier`; only guaranteed to remain unique for lifetime of object.

```swift
class MyClass: Identifiable {}
```

Note that since `Identifiable` has associated type, cannot be directly used as type of func param or property:

```swift
func compare(thing1: Identifiable, with thing2: Identifiable) -> Bool {
    // omitted
}
// compile error: Protocol 'Identifiable' can only be used as a generic constraint because it has Self or associated type requirements 
```

Use specific type, or use it as generic constraint:

```swift
func compare<T: Identifiable>(thing1: T, with thing2: T) -> Bool {
    // omitted
}
```
