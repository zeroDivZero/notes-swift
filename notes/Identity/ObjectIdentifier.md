# `@frozen struct ObjectIdentifier`

With value types, `Equatable` or `Hashable` is probably best to implement identity. With reference types, can use `===` operator (identical instance check), or `ObjectIdentifier`.

```swift
class AnotherClass {}

class SomeClass {
  var obj = AnotherClass()
}

extension SomeClass: Hashable {
    static func == (lhs: SomeClass, rhs: SomeClass) -> Bool {
        lhs.obj === rhs.obj
    }

    func hash(into hasher: inout Hasher) {
        hasher.combine(ObjectIdentifier(obj))
    }
}
```

Only class instances and metatypes have unique identities. There is no notion of identity for structs, enums, functions, or tuples.
