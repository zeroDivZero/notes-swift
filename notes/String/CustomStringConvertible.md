# `protocol CustomStringConvertible`

Type with customized textual representation. Type conforming to `CustomStringConvertible` can provide representation to be used when converting inst to string. `String(describing:)` is preferred way to convert inst to string.

## Conformance

Define `description` property. Before:

```swift
struct Point {
    let x: Int, y: Int
}

let p = Point(x: 21, y: 30)
print(p)
// "Point(x: 21, y: 30)"
```

After:

```swift
extension Point: CustomStringConvertible {
    var description: String {
        "(\(x), \(y))"
    }
}

print(p)
// "(21, 30)"
```

Accessing `description` directly or using `CustomStringConvertible` as generic constraint is discouraged.
