# OVERVIEW

Function or method with opaque return type hides its return value's type info. Instead of concrete type, return value is described by protocols.

Useful at module boundary, keeps underlying return value type private. Unlike returning value whose type is protocol type, opaque type preserves type identity — compiler has access to type info, but not client of module.

## Problem

E.g., module draws ASCII art shapes. Basic characteristic of ASCII art shape is `draw()` that returns string representation of that shape, which you can use as requirement for `Shape` protocol:

```swift
protocol Shape {
    func draw() -> String
}

struct Triangle: Shape {
    var size: Int
    func draw() -> String {
        var result = [String]()
        for length in 1...size {
            result.append(String(repeating: "*", count: length))
        }
        return result.joined(separator: "\n")
    }
}
let smallTriangle = Triangle(size: 3)
print(smallTriangle.draw())
// *
// **
// ***
```
