# To `Data` and Back

## `String` to `Data`

```swift
let strData = Data("Café".utf8)
```

## `Data` to `String`

May fail, never force-unwrap:

```swift
var bytes = strData
bytes.removeLast()
let str = String(data: bytes, encoding: .utf8)!  // fatal error
```

Use another initializer if do not want optional. Inserts "�" if data cannot be decoded.

```swift
let str = String(decoding: bytes, as: UTF8.self)  // "Caf�"
```
