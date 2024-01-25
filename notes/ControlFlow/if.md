# `if`

```swift
if score >= 100 {
    print("Perfect!")
} else if score >= 60 {
    print("Passed.")
} else {
    print("Failed..")
}
```

Conditional must be boolean expression. No implicit zero comparison.

## Optional Binding

```swift
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
else {
    greeting = "No one here."
}
```

Compound conditions:

```swift
var shouldPrint = true
var latitude: Double? = 30
var longitude: Double? = 50
if shouldPrint, let lat = latitude, let lon = longitude {
    print("(\(lat), \(lon))")
}
```

## Pattern Matching (if-case)

`switch` is cumbersome to match single case. Following are equivalent:

```swift
switch direction() {
case .north(let units) where units != 42:
     moveNorth(units)
default: break
}
```

```swift
if case .north(let units) = direction(), units != 42 {
    moveNorth(units)
}
```
