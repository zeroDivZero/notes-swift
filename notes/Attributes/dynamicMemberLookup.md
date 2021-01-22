# `@dynamicMemberLookup`

```swift
@dynamicMemberLookup
struct DataSource {
    let valInt: Int

    struct Immutables {
        let valStr: String
        let valBool: Bool
    }
    let immutables: Immutables

    subscript<T>(dynamicMember member: KeyPath<Immutables, T>) -> T {
        immutables[keyPath: member]
    }
}

let ds = DataSource(valInt: 7, immutables: .init(valStr: "Hello world!", valBool: true))
print(ds.valInt)
print(ds.valStr)
print(ds.valBool)
```
